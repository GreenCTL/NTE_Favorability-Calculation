<template>
  <div class="visitor-counter">
    <img v-if="shouldShowBadge" :src="badgeUrl" alt="瀏覽人次" />
    
    <span v-else class="pure-text-views">瀏覽人次：{{ cachedViews }} 次</span>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const shouldShowBadge = ref(false)
const badgeUrl = ref('')
const cachedViews = ref('---')

onMounted(() => {
  const siteDomain = 'nte-tools-favorability-calculation.netlify.app'
  
  // 1. 檢查這個瀏覽器分頁是否已經算過人數了
  const hasVisited = sessionStorage.getItem('has_visited_this_session')

  if (hasVisited) {
    // 2. 如果算過了（按 F5），我們「絕對不加載」原本的計數圖片！
    // 直接從 localStorage 拿上一次發送成功時存下來的數字，顯示成純文字
    shouldShowBadge.value = false
    cachedViews.value = localStorage.getItem('last_known_views') || '---'
  } else {
    // 3. 如果是第一次進來，載入正常網址（後台自動 +1）
    badgeUrl.value = `https://visitor-badge.laobi.icu/badge?page_id=${siteDomain}`
    shouldShowBadge.value = true
    
    // 標記這張分頁已經看過了
    sessionStorage.setItem('has_visited_this_session', 'true')
    
    // 💡 小技巧：因為我們沒辦法從圖片直接抓到數字，所以我們先預設一個基礎緩存
    // 或者是等圖片載入後，隨時間或手動紀錄（這裡我們主要是為了防止重整畫面變空白）
    localStorage.setItem('last_known_views', '讀取中（請重新整理）')
  }
})
</script>

<style scoped>
.visitor-counter {
  text-align: center;
  padding: 15px;
}
.visitor-counter img {
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
.pure-text-views {
  font-size: 14px;
  color: #666;
  font-weight: bold;
}
</style>