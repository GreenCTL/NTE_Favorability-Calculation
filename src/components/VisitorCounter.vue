<template>
  <div class="visitor-counter">
    <span class="pure-text-views">
      瀏覽人次：{{ cachedViews }} 次
    </span>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const cachedViews = ref('讀取中')

onMounted(async () => {
  const counterKey = 'nte_tools_favorability_calculation_netlify_app_total_views'
  const hasVisited = sessionStorage.getItem('has_visited_this_session')

  const action = hasVisited ? 'get' : 'hit'
  const apiUrl = `https://countapi.mileshilliard.com/api/v1/${action}/${counterKey}`

  try {
    const res = await fetch(apiUrl)
    const data = await res.json()

    if (!res.ok) {
      throw new Error(data.error || '讀取失敗')
    }

    cachedViews.value = Number(data.value).toLocaleString()
    localStorage.setItem('last_known_views', cachedViews.value)

    if (!hasVisited) {
      sessionStorage.setItem('has_visited_this_session', 'true')
    }
  } catch (error) {
    cachedViews.value = localStorage.getItem('last_known_views') || '讀取失敗'
    console.error(error)
  }
})
</script>

<style scoped>
.visitor-counter {
  text-align: center;
  padding: 15px;
}

.pure-text-views {
  font-size: 14px;
  color: #666;
  font-weight: bold;
}
</style>