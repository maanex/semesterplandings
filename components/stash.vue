<template>
  <div class="stash" @dragover.prevent @drop="onDrop">
    <div>
      <Fach v-for="fach in list" :key="fach" :data="getFach(fach)" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { Faecher } from '~/faecher'

defineProps<{
  list: string[]
}>()

const emit = defineEmits<{
  dropped: [ string ]
}>()

function getFach(name: string) {
  return Faecher.find(l => l.name === name)!
}

function onDrop(event: any) {
  const data = event.dataTransfer.getData("fach")
  emit('dropped', data)
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
