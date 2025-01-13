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
      <pre>Durchschnittliche ECTS pro Semester: {{ avgEctsProSemester.toFixed(1) }}
=> Fertig in ca {{ fertigIn }} semestern</pre>

      <span v-for="kat of [...kategorien]" :key="kat">
        {{ kat }}: {{ removeTrailingZeros(semesters.map(s => s.filter(f => f.kategorie === kat).map(f => f.ects).reduce((a, b) => a + b, 0))).join(' + ') || 'keine' }} ECTS
        = {{ semesters.flatMap(s => s.filter(f => f.kategorie === kat).map(f => f.ects)).reduce((a, b) => a + b, 0) }} gesamt
        <br>
      </span>
    </Infobox>
  </div>
</template>

<script setup lang="ts">
export type Fach = {
  name: string
  ects: number
  kategorie: string
}

const stash = useLocalStorage<Fach[]>('data.stash', [
  {
    name: 'Freier Bereich',
    ects: 5,
    kategorie: 'Freier Bereich'
  },
  {
    name: 'Studienbegleitendes fachdidaktisches Praktikum',
    ects: 5,
    kategorie: 'Studienbegleitendes fachdidaktisches Praktikum'
  },
  {
    name: 'Pädagogisch-didaktisches Schulpraktikum',
    ects: 5,
    kategorie: 'Pädagogisch-didaktisches Schulpraktikum'
  },
  {
    name: 'Schulpädagogik I: Grundlagen',
    ects: 5,
    kategorie: 'Schulpädagogik'
  },
  {
    name: 'Schulpädagogik II: Vertiefung schulpädagogischer Fragestellungen',
    ects: 5,
    kategorie: 'Schulpädagogik'
  },
  {
    name: 'Allgemeine Pädagogik I',
    ects: 5,
    kategorie: 'Allgemeine Pädagogik'
  },
  {
    name: 'Allgemeine Pädagogik II',
    ects: 5,
    kategorie: 'Allgemeine Pädagogik'
  },
  {
    name: 'Lernprozesse gestalten',
    ects: 5,
    kategorie: 'Psychologie'
  },
  {
    name: 'Lernermerkmale',
    ects: 5,
    kategorie: 'Psychologie'
  },
  {
    name: 'Vertiefung Lernprozesse und Lernermerkmale',
    ects: 5,
    kategorie: 'Psychologie'
  },
  {
    name: 'Schriftliche Hausarbeit',
    ects: 10,
    kategorie: 'Schriftliche Hausarbeit'
  },
  {
    name: 'Ling BM-1',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Lit BM',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'NdL BM-1',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Ling BM-2',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Med BM',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Ndl BM-2',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Ling AM',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Med AM-Mhd',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Ling AM-2',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Lit AM-L',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Lit AM-G',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Lit AM-W',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Ling VM-1',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Ling VM-2',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Med VM-1',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Med VM-2',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'NdL VM-1',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'NdL VM-2',
    ects: 5,
    kategorie: 'Deutsch'
  },
  {
    name: 'Ling Finit',
    ects: 10,
    kategorie: 'Deutsch'
  },
  {
    name: 'Ndl Finit',
    ects: 10,
    kategorie: 'Deutsch'
  },
  {
    name: 'Med Finit',
    ects: 10,
    kategorie: 'Deutsch'
  },
  {
    name: 'NdL SM',
    ects: 10,
    kategorie: 'Deutsch'
  },
  {
    name: 'Ling SM',
    ects: 10,
    kategorie: 'Deutsch'
  },
  {
    name: 'Med SM',
    ects: 10,
    kategorie: 'Deutsch'
  },
  {
    name: 'BM FDD',
    ects: 5,
    kategorie: 'Deutsch Didaktik'
  },
  {
    name: 'VM FDD',
    ects: 5,
    kategorie: 'Deutsch Didaktik'
  },
  {
    name: 'Alte Geschichte',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Mittelalterliche Geschichte',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Neuere Geschichte',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Neuste Geschichte und Zeitgeschichte',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Basismodul I',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Basismodul II',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Basismodul III',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Basismodul IV',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Methodische Grundlagen',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Theoretische Grundlagen',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Schwerpunkt Historische Forschungspraxis Landesgeschichte',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Schwerpunkt Historisches Fachwissen Landesgeschichte',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Spezialisierungsmodul Landesgeschichte',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Epochenübergreifende Lektüreübung',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Schwerpunkt Historische Forschungspraxis',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Schwerpunkt Historisches Fachwissen',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Schwerpunkt Historische Forschungspraxis II',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Scherpunkt Historisches Fachwissen II',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Spezialisierungsmodul',
    ects: 5,
    kategorie: 'Geschichte'
  },
  {
    name: 'Basismodul Didaktik der Geschichte',
    ects: 5,
    kategorie: 'Geschichte Didaktik'
  },
  {
    name: 'Aufbaumodul Didaktik der Geschichte',
    ects: 5,
    kategorie: 'Geschichte Didaktik'
  },
  {
    name: 'Modernes Chinesisch 1',
    ects: 10,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Geschichte und Kultur Chinas',
    ects: 5,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Modernes Chinesisch 2',
    ects: 10,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Staat und Gesellschaft Chinas',
    ects: 5,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Modernes Chinesisch 3',
    ects: 10,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Modernes Chinesisch: Hören und Sprechen',
    ects: 10,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Modernes Chinesisch 4',
    ects: 5,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Klassisches Chinesisch 1',
    ects: 5,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Modernes Chinensisch 5',
    ects: 5,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Modernes Chinesisch 6',
    ects: 5,
    kategorie: 'Chinesisch'
  },
  {
    name: 'Sprache und Literatur Chinas',
    ects: 5,
    kategorie: 'Chinesisch'
  },
])

const kategorien = new Set(stash.value.map(f => f.kategorie))

const updateSems = ref(0)

function stashDropped(fach: Fach) {
  stash.value.splice(stash.value.findIndex(f => f.name === fach.name), 1)
  stash.value.push(fach)
  semesters.value.forEach(sem => {
    if (sem.some(f => f.name === fach.name))
      sem.splice(sem.findIndex(f => f.name === fach.name), 1)
  })
  updateSems.value++
}

const semesters = useLocalStorage<Array<Fach[]>>('data.semesters', [
  [], [], [], [], [], [], [], [], [], [], [], [], [], []
])

const avgEctsProSemester = computed(() => {
  return semesters.value.reduce((acc, sem) => acc + sem.reduce((acc, f) => acc + f.ects, 0), 0) / semesters.value.filter(s => s.length > 0).length || 0
})

const fertigIn = computed(() => {
  return Math.ceil(271 / avgEctsProSemester.value - 0.2)
})

function semestersDropped(fach: Fach, semester: number) {
  semesters.value.forEach(sem => {
    if (sem.some(f => f.name === fach.name))
      sem.splice(sem.findIndex(f => f.name === fach.name), 1)
  })
  semesters.value[semester].push(fach)
  stash.value.splice(stash.value.findIndex(f => f.name === fach.name), 1)
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
