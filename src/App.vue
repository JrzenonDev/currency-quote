<template>
  <div class="container grid-lg">
  </div>
</template>

<script>
import { onMounted, reactive, toRefs } from 'vue'
import api from '@/services/api'

export default {
  name: 'App',
  setup() {

    const data = reactive({
      quotes: {}
    })

    onMounted(async () => {
      const response = await api.all()
      data.quotes = response.data
    })

    return { ...toRefs(data) }

  }
}
</script>
