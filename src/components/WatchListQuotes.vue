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
import { onMounted, onUnmounted, watch } from '@vue/runtime-core'

const CURRENT_UPDATE_TIME = 30

export default {
  name: 'WatchListQuotes',
  components: { ListQuotes },
  props: {
    listenQuotes: {
      type: Array,
      required: true
    }
  },
  emits: ['unListen'],
  setup(props, {emit}) {
    const interval = ref(null)
    const quotes = ref({})
    const nextUpdateTime = ref(CURRENT_UPDATE_TIME)

    const methods = reactive({
      onUnListen(code) {
        emit('unListen', code)
      },

      restartInterval() {
        clearInterval(interval.value)
        nextUpdateTime.value = CURRENT_UPDATE_TIME
        interval.value = setInterval(() => {
          nextUpdateTime.value -= 1
          if (nextUpdateTime.value === 0) {
            nextUpdateTime.value = CURRENT_UPDATE_TIME
            this.refresh()
          }
        }, 1000)
      },

      async refresh() {
        const {data} = await api.listen(props.listenQuotes)
        quotes.value = data
      }
    })

    onUnmounted(() => {
      clearInterval(interval.value)
    })

    onMounted(() => {
      methods.refresh()
      methods.restartInterval()
    })

    watch(props, () => {
      methods.refresh()
      methods.restartInterval()
    })

    return {
        ...toRefs(methods),
        quotes,
        nextUpdateTime
      }
  }
}
</script>