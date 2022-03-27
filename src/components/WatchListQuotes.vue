<template>
  <ListQuotes :quotes="quotes" />
</template>

<script>
import { ref } from '@vue/reactivity'
import ListQuotes from './ListQuotes'

export default {
  name: 'WatchListQuotes',
  components: { ListQuotes },
  setup() {
    const quotes = ref({})

    return { quotes }
  }
}
</script>