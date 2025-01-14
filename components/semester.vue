<template>
  <div
    class="sem"
    @dragover.prevent
    @drop="onDrop"
    :data-fertig="fertig"
  >
    <span>{{ (totalEcts/semester).toFixed(1) }}</span>
    <span class="semestersl">{{ semester }}</span>
    <span>{{ ectsGemachtNormal }} + {{ ectsGemachtChinesisch }}</span>
    <div class="drop">
      <Fach v-for="fach in fachs" :key="fach" :data="getFach(fach)!" :backup-name="fach" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { Faecher } from '~/faecher'

const props = defineProps<{
  semester: number
  totalEcts: number
  fertig: boolean
  fachs: string[]
}>()

function getFach(name: string) {
  return Faecher.find(l => l.name === name)
}

const ectsGemachtNormal = computed(() => props.fachs.filter(f => !getFach(f)?.kategorie.toLowerCase().includes('chinesisch')).reduce((acc, f) => acc + (getFach(f)?.ects ?? 0), 0))
const ectsGemachtChinesisch = computed(() => props.fachs.filter(f => getFach(f)?.kategorie.toLowerCase().includes('chinesisch')).reduce((acc, f) => acc + (getFach(f)?.ects ?? 0), 0))

const emit = defineEmits<{
  dropped: [ string ]
}>()

function onDrop(event: any) {
  const data = event.dataTransfer.getData("fach")
  emit('dropped', data)
}
</script>

<style scoped lang="scss">
.sem {
  &[data-fertig=true] {
    .semestersl {
      background-color: lightgreen;
    }
  }

  .drop {
    padding: 5pt;
    display: flex;
    flex-wrap: wrap;
    gap: 3pt;
  }
}
</style>
