<template>
  <div class="visitor-counter">
    <iframe
      v-if="!isF5Refresh"
      :src="`https://twcc.pureconcept.club/v1/hit/${slug}/record.svg`"
      width="160"
      height="30"
      frameborder="0"
      scrolling="no"
      style="overflow:hidden;"
    ></iframe>

    <div v-else class="pure-text-views">
      <span>📊 瀏覽人次已記錄</span>
      <p class="tip-text">（同一次訪問內重複整理不重複計算）</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const slug = 'nte-tools-favorability'
const isF5Refresh = ref(false)

onMounted(() => {
  // 檢查這個分頁是否已經算過人數了
  const hasVisited = sessionStorage.getItem('has_visited_this_session')

  if (hasVisited) {
    // 如果算過了（按 F5），我們啟用物理防刷：直接不渲染 iframe！
    isF5Refresh.value = true
  } else {
    // 如果是第一次進來，標記分頁，並讓 iframe 顯示（去後台 +1）
    isF5Refresh.value = false
    sessionStorage.setItem('has_visited_this_session', 'true')
  }
})
</script>

<style scoped>
.visitor-counter {
  text-align: center;
  padding: 15px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.pure-text-views {
  font-size: 14px;
  color: #4caf50;
  font-weight: bold;
}
.tip-text {
  font-size: 11px;
  color: #999;
  margin: 4px 0 0 0;
  font-weight: normal;
}
</style>