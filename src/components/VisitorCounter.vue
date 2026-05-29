<template>
  <div class="visitor-counter">
    <p>瀏覽人數：<span>{{ views }}</span> 次</p>
  </div>
</template>
<script setup>
import { ref, onMounted } from 'vue'

const views = ref('載入中...')

onMounted(async () => {
  const namespace = 'NTE-tools-Favorability'
  const key = 'homepage'

  // 1. 檢查這個瀏覽器分頁是否已經算過人數了
  const hasVisited = sessionStorage.getItem('has_visited_this_session')

  if (hasVisited) {
    // 2. 如果算過了，只讀取數字，不增加（使用 countapi 的 get 接口）
    try {
      const response = await fetch(`https://api.countapi.xyz/get/${namespace}/${key}`)
      const data = await response.json()
      views.value = data.value
    } catch (error) {
      views.value = '---'
    }
  } else {
    // 3. 如果是第一次進來，呼叫 hit 接口讓數字 +1
    try {
      const response = await fetch(`https://api.countapi.xyz/hit/${namespace}/${key}`)
      const data = await response.json()
      views.value = data.value
      
      // 標記這張分頁已經看過了
      sessionStorage.setItem('has_visited_this_session', 'true')
    } catch (error) {
      views.value = '---'
    }
  }
})
</script>