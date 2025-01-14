<template>
  <div class="fach" draggable="true" @dragstart="onDragStart">
    <span>{{ data.name }}</span>
    <span>{{ data.ects }} ECTS</span>
  </div>
</template>

<script setup lang="ts">
import type { Fach } from '~/app.vue'

const props = defineProps<{
  data: Fach
}>()

function onDragStart(event: any) {
  event.dataTransfer.setData("fach", toRaw(unref(props.data.name)));
}

const color = computed(() => {
  const hue = (props.data.kategorie.split('').reduce((acc, char) => acc + char.charCodeAt(0), 0) * 137) % 360
  return `hsl(${hue}, 70%, 70%)`
})
</script>

<style scoped lang="scss">
.fach {
  display: inline-flex;
  flex-direction: column;
  height: fit-content;
  padding: 4pt 11pt;
  border-radius: 999pt;
  border: none;
  background-color: v-bind(color);
  cursor: pointer;
  
  &:hover {
    background-color:  #bbbbbb;
  }

  span {
    width: 100%;
    text-align: center;
  }

  span:first-child {
    font-weight: 600;
    font-size: 10pt;
    font-family: sans-serif;
  }

  span:last-child {
    font-weight: 400;
    font-size: 9pt;
    font-family: monospace;
  }
}
</style>