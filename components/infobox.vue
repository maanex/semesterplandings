<template>
  <div class="infobox">
    <slot />
  </div>
</template>

<script setup lang="ts">

</script>

<style scoped lang="scss">
.infobox {
  font-family: monospace;
  border: 1px solid black;
  height: 30vh;
  box-sizing: border-box;
  overflow-y: scroll;
}
</style>
