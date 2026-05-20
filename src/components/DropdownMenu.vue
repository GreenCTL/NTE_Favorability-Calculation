<template>
    <!-- 下拉式選單 -->
    <div class="dropdown-container" ref="dropdownRef">

        <button @click.stop="toggleDropdown" class="dropdown-btn" type="button" :aria-expanded="isOpen">
            {{ modelValue?.label || label }}
            <span class="arrow" :class="{ 'is-reverse': isOpen }">▼</span>
        </button>

        <Transition name="fade">

            <ul v-if="isOpen" class="dropdown-menu" ref="listRef">
                <li v-for="item in items" :key="item.label" @click="handleSelect(item)"
                    :class="['dropdown-item', { 'is-active': item.label === modelValue?.label }]">
                    <img v-if="item?.icon" :src="item.icon" alt="icon" referrerpolicy="no-referrer" class="menu-avatar">
                    <span>{{ item.label }}</span>
                </li>
            </ul>
        </Transition>
    </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch, nextTick } from 'vue'

const props = defineProps({
    label: { type: String, default: '請選擇' },
    items: { type: Array, required: true },
    modelValue: { type: Object, default: null } // 接收父層的 v-model
})

const emit = defineEmits(['update:modelValue'])

const isOpen = ref(false)
const dropdownRef = ref(null)
//獲取ul的DOM
const listRef = ref(null)
const toggleDropdown = () => { isOpen.value = !isOpen.value }

const handleSelect = (item) => {
    emit('update:modelValue', item) // 修正：更新父層的 v-model 值
    isOpen.value = false // 關閉選單
}

const handleClickOutside = (event) => {
    //檢查點擊目標是否不再組件範圍內
    if (dropdownRef.value && !dropdownRef.value.contains(event.target)) {
        isOpen.value = false // 關閉選單
    }
}
//監聽選單打開的動作
watch(isOpen, async (newVal) => {
    if (newVal && props.modelValue) {
        // 必須等待 Vue 完成渲染 (nextTick)，才能抓到裡面的 <li>
        await nextTick()
        // 找到具有 .is-active 類別的元素
        const activeItem = dropdownRef.value?.querySelector('.is-active')
        if (activeItem) {
            activeItem.scrollIntoView({
                block: 'nearest',
                behavior: 'smooth'
            })
        }
    }
})
onMounted(() => {
    document.addEventListener('pointerdown', handleClickOutside)
})
onUnmounted(() => {
    document.removeEventListener('pointerdown', handleClickOutside)
})
</script>

<style scoped>
.dropdown-container {
    position: relative;
    width: 200px;
    display: inline-block;
}

.dropdown-btn {
    width: 100%;
    padding: 10px;
    background-color: #35495e;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    text-align: left;
}

.dropdown-btn:hover {
    background-color: #496079;
    transform: translateY(-3px);
    /* 向上微移 */
    box-shadow: 0 10px 20px rgba(95, 154, 231, 0.2);
    /* 增加發光的青色陰影 */
}

.dropdown-btn:active {
    background-color: #496079;
    box-shadow: 0 10px 20px rgba(95, 154, 231, 0.2);
    /* 增加發光的青色陰影 */
}

.dropdown-menu {
    position: absolute;
    top: 100%;
    left: 0;
    width: 100%;
    background-color: white;
    border: 1px solid #ccc;
    border-radius: 4px;
    list-style: none;
    padding: 0;
    margin: 5px 0 0 0;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    z-index: 10;
    max-height: 200px;
    overflow-y: auto;
}

.dropdown-item {
    display: flex;
    align-items: center;
    gap: 10px;
    /* 圖片和文字的間距 */
    padding: 10px;
    cursor: pointer;
    color: #070707;
}

.dropdown-item:hover {
    background-color: #4583ca;
}

/* 選單內的頭像樣式 */
.menu-avatar {
    width: 25px;
    height: 25px;
    border-radius: 50%;
    object-fit: cover;
}

.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.2s, transform 0.2s;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
    transform: translateY(-10px);
}

.is-active {
    background-color: #98b4d4;
    /* 給予選中項輕微底色 */
    color: #080808;
}

.arrow {
    display: inline-block;
    transition: transform 0.3s;
    font-size: 0.8rem;
}

.arrow.is-reverse {
    transform: rotate(180deg);
}
</style>