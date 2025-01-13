<template>
  <div class="stash" @dragover.prevent @drop="onDrop">
    <div>
      <Fach v-for="fach in list" :key="fach.name" :data="fach" />
    </div>
  </div>
</template>

<script setup lang="ts">
import type { Fach } from '~/app.vue'

defineProps<{
  list: Fach[]
}>()

const emit = defineEmits<{
  dropped: [ Fach ]
}>()

function onDrop(event: any) {
  const data = event.dataTransfer.getData("fach")
  emit('dropped', JSON.parse(data) as Fach)
}
</script>

<style scoped lang="scss">
.stash {
  border: 1px solid black;
  padding: 10pt;
  height: 100vh;
  box-sizing: border-box;
  overflow-y: scroll;

  & > div {
    display: flex;
    flex-wrap: wrap;
    gap: 5pt;
  }
}
</style>
