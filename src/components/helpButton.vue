<template>
  <div class="help-wrapper">

    <!-- 問號按鈕 -->
    <button
      class="help-btn"
      @mouseenter="showTooltip = true"
      @mouseleave="showTooltip = false"
      @click="toggleModal"
    >
      ?
    </button>

    <!-- hover 提示 -->
    <div
      v-if="showTooltip"
      class="tooltip"
    >
      禮物好感度一覽
    </div>

    <!-- 放大圖片 Modal -->
     <Teleport to ="body">
     
    <div
      v-if="showModal"
      class="modal-overlay"
      @click="closeModal"
    >

      <div
        class="modal-content"
        @click.stop
      >

        <button
          class="close-btn"
          @click="closeModal"
        >
          ✕
        </button>

        <img
          :src="image"
          class="preview-image"
        >

      </div>

    </div>
</Teleport>
  </div>
</template>
<script setup>
import { ref } from 'vue'

const props = defineProps({
  image: String
})

const showTooltip = ref(false)
const showModal = ref(false)

const toggleModal = () => {
  showModal.value = true
}

const closeModal = () => {
  showModal.value = false
}
</script>
<style scoped>
.help-wrapper {
  position: relative;
  display: inline-block;
}

/* 問號按鈕 */
.help-btn {
  width: 100%;
  height: 50%;
  border-radius: 50%;
  border: none;
  background: #4a90e2;
  color: white;
  font-size: 30%;
  font-weight: bold;
  cursor: pointer;
  transition: .2s;
}

.help-btn:hover {
  transform: scale(1.1);
  background: #3578d6;
}
/* hover 提示 */
.tooltip {
  position: absolute;

  top: 40px;
  left: 50%;

  transform: translateX(-50%);

  background: rgba(0,0,0,.8);
  color: white;

  padding: 8px 12px;

  border-radius: 8px;

  white-space: nowrap;

  font-size: 14px;

  z-index: 20;
}

/* 背景遮罩 */
.modal-overlay {
  position: fixed;
  inset: 0;
  display: flex;
  background: rgba(0,0,0,.75);

  display: flex;
  align-items: center;
  justify-content: center;

  z-index: 999;

  animation: fadeIn .25s ease;
}

/* 內容區 */
.modal-content {
  position: relative;

  max-width: 90vw;
  max-height: 90vh;

  animation: zoomIn .25s ease;
}
/* 圖片 */
.preview-image {
  max-width: 90vw;
  max-height: 90vh;

  object-fit: contain;

  border-radius: 16px;

  box-shadow: 0 0 30px rgba(0,0,0,.5);
}

/* 關閉按鈕 */
.close-btn {
  position: absolute;
  color: white;
  top: -12px;
  right: -12px;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: white 2px solid;
  background: rgb(0, 0, 0);
  font-size: 18px;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(0,0,0,.3);
}
.close-btn:hover{
      color: rgb(173, 173, 173);
      background-color: rgb(29, 28, 28);
}
/* 動畫 */
@keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

@keyframes zoomIn {
  from {
    transform: scale(.8);
    opacity: 0;
  }

  to {
    transform: scale(1);
    opacity: 1;
  }
}
</style>