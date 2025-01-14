<template>
  <div class="app">
    <Semesters
      :key="updateSems"
      :semesters
      :fertig-in="fertigIn"
      @dropped="semestersDropped"
    />
    <Stash
      :list="stash"
      :key="updateSems"
      @dropped="stashDropped"
    />
    <Infobox>
      <pre>Durchschnittliche ECTS pro Semester: {{ avgEctsProSemester.toFixed(1) }} (Chinesisch ausgenommen)
=> Fertig in ca {{ fertigIn }} semestern</pre>

      <span v-for="kat of [...kategorien]" :key="kat">
        {{ kat }}: {{ removeTrailingZeros(semesters.map(s => s.filter(f => getFach(f)?.kategorie === kat).map(f => getFach(f)?.ects ?? 0).reduce((a, b) => a + b, 0))).join(' + ') || 'keine' }} ECTS
        = {{ semesters.flatMap(s => s.filter(f => getFach(f)?.kategorie === kat).map(f => getFach(f)?.ects ?? 0)).reduce((a, b) => a + b, 0) }} gesamt
        <br>
      </span>
    </Infobox>
  </div>
</template>

<script setup lang="ts">
import { Faecher } from './faecher'

export type Fach = {
  name: string
  ects: number
  kategorie: string
}

const stash = useLocalStorage<string[]>('data2.stash', Faecher.map(f => f.name))

const semesters = useLocalStorage<Array<string[]>>('data2.semesters', [
  [], [], [], [], [], [], [], [], [], [], [], [], [], []
])

const kategorien = new Set(Faecher.map(f => f.kategorie))

const updateSems = ref(0)

function stashDropped(fach: string) {
  removeFromAllSems(fach)
  updateStashValue()
}

function getFach(name: string) {
  return Faecher.find(l => l.name === name)
}

const avgEctsProSemester = computed(() => {
  const filteredSemesters = semesters.value.map(sem => sem.filter(f => !getFach(f)?.kategorie.toLowerCase().includes('chinesisch')))
  return filteredSemesters.reduce((acc, sem) => acc + sem.reduce((acc, f) => acc + (getFach(f)?.ects ?? 0), 0), 0) / filteredSemesters.filter(s => s.length > 0).length || 0
})

const fertigIn = computed(() => {
  return Math.ceil(271 / avgEctsProSemester.value - 0.2)
})

function semestersDropped(fach: string, semester: number) {
  removeFromAllSems(fach)
  if (getFach(fach))
    semesters.value[semester].push(fach)
  updateStashValue()
}

function removeFromAllSems(fach: string) {
  for (const sem of semesters.value) {
    for (const check of sem) {
      if (!getFach(check) || check === fach)
        sem.splice(sem.indexOf(check), 1)
    }
  }
}

function updateStashValue() {
  stash.value = Faecher.map(f => f.name).filter(name => semesters.value.every(sem => !sem.includes(name)))
  updateSems.value++
}

function removeTrailingZeros(arr: number[]) {
  if (!arr) return []
  let i = arr.length - 1
  while (arr[i] === 0) {
    i--
  }
  return arr.slice(0, i + 1)
}
</script>

<style scoped lang="scss">
.app {
  display: grid;
  grid-template-columns: 1fr 30vw;
  height: 100vh;
  box-sizing: border-box;
}
</style>

<style lang="scss">
body {
  padding: 0;
  margin: 0;
}

.stash {
  grid-column: 2;
  grid-row: 1 / span 2
}

.sem {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 8fr;
  border-bottom: 1px solid #bbbbbb;

  & > * {
    padding: 10pt;
  }

  & > :not(:last-child) {
    border-right: 1px solid #bbbbbb;
  }
}
</style>
