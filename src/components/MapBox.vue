<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import mapboxgl from 'mapbox-gl'

/*
Token
*/
const mapToken = import.meta.env.VITE_MAPBOX_TOKEN as string

const mapContainer = ref<HTMLElement | null>(null)
const map = ref<mapboxgl.Map | null>(null)

interface StoreData {
  name: string
  address: string
  coordinates: number[]
}

const mockUpData = ref<StoreData[]>([
  {
    name: 'Covent Garden',
    address: '6 Slingsby Place, London, WC2E 9AB',
    coordinates: [51.5127248, -0.1287614], // [lat, lng]
  },
  {
    name: 'Mayfair',
    address: 'The House of ELEMIS, 2 Lancashire Court, London, W1S 1EX',
    coordinates: [51.5125376, -0.1478229], // [lat, lng]
  },
  {
    name: 'Victoria',

    address: 'John Lewis & Partners, 171 Victoria St, London SW1E 5NN',
    coordinates: [51.495898, -0.142044],
  },
])

/*
Intialized map and add markers
*/
onMounted(() => {
  if (!mapContainer.value) {
    console.error('Map container element not found.')
    return
  }
  if (!mapToken) {
    console.error('Mapbox Access Token is missing. Check your .env file (VITE_MAPBOX_TOKEN).')
    return
  }

  mapboxgl.accessToken = mapToken

  map.value = new mapboxgl.Map({
    container: mapContainer.value,
    style: 'mapbox://styles/mapbox/streets-v12',
    center: [-0.13, 51.51],
    zoom: 11,
  })

  map.value.on('load', () => {
    if (!map.value) return

    // Loop through the mock data
    mockUpData.value.forEach((store) => {
      const coordinates = store.coordinates

      const mapboxCoordinates: [number, number] = [coordinates[1], coordinates[0]]

      // Create a popup
      const popup = new mapboxgl.Popup({ offset: 25 }) // offset avoids popup covering marker icon
        .setHTML(`<h3>${store.name}</h3><p>${store.address}</p>`)

      // Create a marker for each store
      new mapboxgl.Marker().setLngLat(mapboxCoordinates).setPopup(popup).addTo(map.value)
    })
  })

  // Optional: Add navigation controls (zoom, rotation)
  map.value.addControl(new mapboxgl.NavigationControl())
})

/*
Remove map
*/
onUnmounted(() => {
  if (map.value) {
    map.value.remove()
    map.value = null
  }
})
</script>

<template>
  <div ref="mapContainer" class="map-container"></div>
</template>

<style scoped>
.map-container {
  flex: 1;
  width: 100%;
  height: 100%;
}

/* Optional: Style popups if needed (Mapbox default styles are usually okay) */
/* Make sure Mapbox CSS is imported for these selectors to work */
:deep(.mapboxgl-popup-content) {
  font-family: sans-serif;
  padding: 10px 10px 15px;
}

:deep(.mapboxgl-popup-content h3) {
  margin: 0 0 5px;
  font-size: 1.1em;
  color: #333;
}

:deep(.mapboxgl-popup-content p) {
  margin: 0;
  font-size: 0.9em;
  color: #555;
}
</style>
