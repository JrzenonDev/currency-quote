<template>
  <ListQuotes
    :quotes="quotes"
    :listen-quotes="listenQuotes"
    @unListen="onUnListen"
  />
  <div class="mt-2 text-right">
    <cite class="text-small">
      Atualizará novamente em <b>{{ nextUpdateTime }} segundos</b>
    </cite>
  </div>
</template>

<script>
import { reactive, ref, toRefs } from '@vue/reactivity'
import ListQuotes from './ListQuotes'
import api from '@/services/api'
import { onMounted, watch } from '@vue/runtime-core'

export default {
  name: 'WatchListQuotes',
  components: { ListQuotes },
  props: {
    listenQuotes: {
      type: Array,
      required: true
    }
  },
  setup(props, {emit}) {
    // const interval = ref(null)
    const quotes = ref({})
    const nextUpdateTime = ref(30)

    const methods = reactive({
      onUnListen(code) {
        emit('unListen', code)
      },

      async refresh() {
        const {data} = await api.listen(props.listenQuotes)
        quotes.value = data
      }
    })

    onMounted(() => {
      methods.refresh()
    })

    watch(props, () => {
      methods.refresh()
    })

    return {
        ...toRefs(methods),
        quotes,
        nextUpdateTime
      }
  }
}
</script>