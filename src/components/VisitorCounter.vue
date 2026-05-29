<script setup>
import { ref, onMounted } from 'vue'

const views = ref('載入中...')

onMounted(async () => {
  // 定義你獨一無二的 ID (改成英文與連字號)

  const slug = 'nte-tools-favorability'
  const ID = 'nte-tools-favorability-homepage'

  const hasVisited = sessionStorage.getItem('has_visited_this_session')

  if (hasVisited) {
    // 2. 唯讀：只獲取目前數字，不遞增
    try {
      const response = await fetch(`https://api.counterapi.dev/v1/${slug}`)
      const data = await response.json()
      // 注意：這家 API 回傳的格式是 { count: 123 }
      views.value = data.count
    } catch (error) {
      console.error('讀取計數失敗：', error)
      views.value = '---'
    }
  } else {
    // 3. 計數+1：呼叫 up 接口讓數字加 1
    try {
      const response = await fetch(`https://api.counterapi.dev/v1/${slug}/up`)
      const data = await response.json()
      views.value = data.count
      
      sessionStorage.setItem('has_visited_this_session', 'true')
    } catch (error) {
      console.error('增加計數失敗：', error)
      views.value = '---'
    }
  }
})
</script>