<template>
  <div class="visitor-counter" v-if="badgeUrl">
    <img :src="badgeUrl" alt="瀏覽人次" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const badgeUrl = ref('')

onMounted(() => {
  const siteDomain = 'nte-tools-favorability-calculation.netlify.app'
  
  // 1. 檢查這個瀏覽器分頁是否已經算過人數了
  const hasVisited = sessionStorage.getItem('has_visited_this_session')

  if (hasVisited) {
    // 2. 如果算過了，按 F5 重新整理時，我們呼叫「只看結果，不計數」的網址
    // 透過在最後加上 &count=false，告訴伺服器這個人只是看看而已，別再加 1 了！
    badgeUrl.value = `https://visitor-badge.laobi.icu/badge?page_id=${siteDomain}&count=false`
  } else {
    // 3. 如果是第一次進來，載入正常網址（後台會自動 +1）
    badgeUrl.value = `https://visitor-badge.laobi.icu/badge?page_id=${siteDomain}`
    
    // 標記這張分頁已經看過了
    sessionStorage.setItem('has_visited_this_session', 'true')
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
</style>