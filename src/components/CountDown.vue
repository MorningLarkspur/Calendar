<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  goal: Date
})
const listKey = ref(0)
const now = ref(new Date().getTime())
const remaining = ref(props.goal - now.value)
const countDown = computed(() => {
  const sec = Math.floor(remaining.value / 1000)
  const min = Math.floor(sec / 60)
  const hou = Math.floor(min / 60)
  const day = Math.floor(hou / 24)
  if (remaining.value < 0) {
    return 'Done'
  } else {
    return (
      day +
      ':' +
      String(hou % 24).padStart(2, '0') +
      ':' +
      String(min % 60).padStart(2, '0') +
      ':' +
      String(sec % 60).padStart(2, '0')
    )
  }
})
function updateTimer() {
  now.value = new Date().getTime()
  remaining.value = props.goal - now.value
}
onMounted(() => {
  listKey.value = setInterval(updateTimer, 1000)
})
onUnmounted(() => {
  clearInterval(listKey)
})
</script>

<template>
  <div>
    <div>{{ countDown }}</div>
  </div>
</template>
