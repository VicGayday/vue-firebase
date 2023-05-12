<template>
  <div class="alert" v-if="alertInfo" :class="alertInfo.type">
  <h3>{{  alertInfo.title }}</h3>
  <p>{{ alertInfo.text }}</p>
  <button class="btn" :class="alertInfo.type" @click="emitClose">Закрыть</button>
  </div>
</template>
<script>
import { defineComponent, computed } from 'vue';

export default defineComponent({
  name: "AppAlert",
  props: ['alert'],
  emits: ['close'],
  setup(props, { emit }) {
    const alertInfo = computed(() => {
      return props.alert
    })

    const emitClose = () => {
     emit('close')
    }

  return {
    alertInfo,
    emitClose,
  }
  }
})
</script>