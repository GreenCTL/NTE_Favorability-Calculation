<template>
  <div class="visitor-counter">
    <span>瀏覽人次：{{ views }} 次</span>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const views = ref('載入中...')

onMounted(async () => {
  // 1. 確保這個名字跟你的 CounterAPI 後台右邊的 Slug 完全一致
  const slug = 'nte-tools-favorability' 

  // 2. 檢查 Session 防刷小筆記
  const hasVisited = sessionStorage.getItem('has_visited_this_session')

  if (hasVisited) {
    // 3. 按 F5 重新整理：呼叫唯讀網址 (加上 JSONP 格式，徹底打破 CORS 封鎖！)
    try {
      const response = await fetch(`https://api.counterapi.dev/v1/${slug}?callback=jsonp`)
      const text = await response.text()
      
      // 解析 JSONP 格式（把包裹在外面的 jsonp(...) 剝掉，只留下裡面的數字）
      const jsonString = text.replace(/^jsonp\((.*)\);?$/, '$1')
      const data = JSON.parse(jsonString)
      
      views.value = data.count
    } catch (error) {
      console.error('F5讀取失敗：', error)
      views.value = '---'
    }
  } else {
    // 4. 第一次進入網頁：呼叫加 1 網址 (同樣帶上 JSONP 確保不卡 CORS)
    try {
      const response = await fetch(`https://api.counterapi.dev/v1/${slug}/up?callback=jsonp`)
      const text = await response.text()
      
      const jsonString = text.replace(/^jsonp\((.*)\);?$/, '$1')
      const data = JSON.parse(jsonString)
      
      views.value = data.count
      
      // 成功加 1 後，記錄到 session 裡，下次按 F5 就會走上面的唯讀路由
      sessionStorage.setItem('has_visited_this_session', 'true')
    } catch (error) {
      console.error('初次計數失敗：', error)
      views.value = '---'
    }
  }
})
</script>

<style scoped>
.visitor-counter {
  text-align: center;
  padding: 15px;
  font-size: 14px;
  color: #666;
  font-weight: bold;
}
</style>