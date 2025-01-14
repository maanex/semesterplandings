<template>
  <div class="infobox">
    <slot />
  </div>
</template>

<script setup lang="ts">

</script>

<style scoped lang="scss">
.infobox {
  font-family: 'Montserrat';
  border-top: 1px solid black;
  padding: 10pt;
  height: 30vh;
  box-sizing: border-box;
  overflow-y: scroll;

  * {
    font-family: 'Montserrat';
    font-size: 11pt;
    line-height: 2;
  }
}
</style>
