<script setup lang="ts">
import MapBox from './components/MapBox.vue'
import LocationPins from './components/LocationPins.vue'
</script>

<template>
  <div class="">
    <LocationPins />
    <MapBox />
  </div>
</template>

<style scoped>
#layout {
  flex: 1;
  display: flex;
  position: relative;
}
</style>
