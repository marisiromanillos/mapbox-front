<script setup lang="ts">
import { ref } from 'vue'

interface StoreData {
  name: string
  address: string
  coordinates: number[]
}

const mockUpData = ref<StoreData[]>([
  {
    name: 'Covent Garden',
    address: '6 Slingsby Place, London, WC2E 9AB',
    coordinates: [51.5127248, -0.1287614],
  },
  {
    name: 'Mayfair',
    address: 'The House of ELEMIS, 2 Lancashire Court, London, W1S 1EX',
    coordinates: [51.5125376, -0.1478229],
  },
  {
    name: 'Victoria',
    address: '129 Victoria Street, London, SW1E 6RD',
    coordinates: [51.5119187, -0.1028299],
  },
])
</script>

<template>
  <div v-for="pin in mockUpData" :key="pin.name">
    <div>
      <p>{{ pin.name }}</p>
      <p>{{ pin.address }}</p>
    </div>
  </div>
</template>
