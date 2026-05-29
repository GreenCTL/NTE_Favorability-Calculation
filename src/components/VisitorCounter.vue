<script setup>
import { ref, onMounted } from 'vue'

const views = ref('載入中...')

onMounted(async () => {
  // 將你的專案識別碼組合起來（新 API 通常只要填一個獨特名稱即可）
  const siteName = 'NTE-tools-Favorability-homepage'

  // 1. 保留：檢查這個瀏覽器分頁是否已經算過人數了
  const hasVisited = sessionStorage.getItem('has_visited_this_session')

  if (hasVisited) {
    // 2. 保留：如果算過了，只讀取數字，不增加 (使用新 API 的 get 接口)
    try {
      const response = await fetch(`https://api.ly93.cc/counter/get/${siteName}`)
      const data = await response.json()
      // 新 API 回傳的欄位名稱通常是 count
      views.value = data.count
    } catch (error) {
      console.error('讀取計數失敗：', error)
      views.value = '---'
    }
  } else {
    // 3. 保留：如果是第一次進來，呼叫 hit 接口讓數字 +1 (使用新 API 的 hit 接口)
    try {
      const response = await fetch(`https://api.ly93.cc/counter/hit/${siteName}`)
      const data = await response.json()
      views.value = data.count
      
      // 保留：標記這張分頁已經看過了
      sessionStorage.setItem('has_visited_this_session', 'true')
    } catch (error) {
      console.error('增加計數失敗：', error)
      views.value = '---'
    }
  }
})
</script>