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
      <Fach v-for="fach in fachs" :key="fach.name" :data="fach" />
    </div>
  </div>
</template>

<script setup lang="ts">
import type { Fach } from '~/app.vue'

const props = defineProps<{
  semester: number
  totalEcts: number
  fertig: boolean
  fachs: Fach[]
}>()

const ectsGemachtNormal = computed(() => props.fachs.filter(f => f.kategorie !== 'Chinesisch').reduce((acc, f) => acc + f.ects, 0))
const ectsGemachtChinesisch = computed(() => props.fachs.filter(f => f.kategorie === 'Chinesisch').reduce((acc, f) => acc + f.ects, 0))

const emit = defineEmits<{
  dropped: [ Fach ]
}>()

function onDrop(event: any) {
  const data = event.dataTransfer.getData("fach")
  emit('dropped', JSON.parse(data) as Fach)
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
