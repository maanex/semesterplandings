<template>
  <div class="sems">
    <div class="sem">
      <span>ECTS pro Semester</span>
      <span>Fertig in</span>
      <span>ECTS gemacht</span>
      <span>Fächer</span>
    </div>
    <Semester
      v-for="sem, idx of semesters"
      :key="idx"
      :semester="idx+1"
      :total-ects="271"
      :fachs="sem"
      :fertig="fertigIn === idx+1"
      @dropped="emit('dropped', $event, idx)"
    />
  </div>
</template>

<script setup lang="ts">
import type { Fach } from '~/app.vue'

defineProps<{
  semesters: Array<string[]>
  fertigIn: number
}>()

const emit = defineEmits<{
  dropped: [ string, number ]
}>()
</script>

<style scoped lang="scss">
.sems {
  display: flex;
  flex-direction: column;
  overflow-y: scroll;
  height: 70vh;
}
</style>
