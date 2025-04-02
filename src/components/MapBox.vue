<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import mapboxgl, { LngLatLike } from 'mapbox-gl' // Import LngLatLike type if needed

/*
Token
*/
const mapToken = import.meta.env.VITE_MAPBOX_TOKEN as string
mapboxgl.accessToken = mapToken

const mapContainer = ref<HTMLElement | null>(null)
const map = ref<mapboxgl.Map | null>(null)

// --- Define Marker Data ---
// In a real app, this might come from an API, props, or a store
interface MarkerInfo {
  id: number
  lngLat: LngLatLike // [longitude, latitude]
  imageUrl: string
  description: string
  size?: number // Optional size in pixels
}

const customMarkersData: MarkerInfo[] = [
  {
    id: 1,
    lngLat: [126.917959, 37.550503], // Original center
    imageUrl: '/path/to/your/custom-icon-1.png', // <<< CHANGE THIS
    description: 'This is Marker 1!',
    size: 40, // 40x40 pixels
  },
  {
    id: 2,
    lngLat: [126.925, 37.555], // Slightly different location
    imageUrl: '/path/to/your/another-icon.svg', // <<< CHANGE THIS
    description: 'Hello from Marker 2',
    size: 35, // 35x35 pixels
  },
  // Add more markers as needed
]
// --- End Marker Data ---

/*
Intialized map
*/
onMounted(() => {
  if (!mapContainer.value) return

  map.value = new mapboxgl.Map({
    container: mapContainer.value,
    style: 'mapbox://styles/mapbox/streets-v12',
    center: [126.917959, 37.550503], // Initial map center
    zoom: 12,
  })

  // --- Add Custom Markers ---
  map.value.on('load', () => {
    // Ensure map style is loaded before adding markers
    if (!map.value) return // Type guard for map.value

    customMarkersData.forEach((markerInfo) => {
      // 1. Create a DOM element for the marker
      const el = document.createElement('div')
      el.className = 'custom-marker' // Assign a class for styling
      el.style.backgroundImage = `url(${markerInfo.imageUrl})`
      const size = markerInfo.size || 30 // Default size if not provided
      el.style.width = `${size}px`
      el.style.height = `${size}px`
      el.style.backgroundSize = '100%' // Ensure the image covers the element
      // Optional: Add event listeners directly to the element if needed
      // el.addEventListener('click', () => alert(`Clicked ${markerInfo.description}`));

      // 2. Create a Mapbox Popup (optional)
      const popup = new mapboxgl.Popup({ offset: 25 }) // Offset avoids covering marker
        .setText(markerInfo.description)
      // Or use .setHTML("<strong>Hello</strong> world!") for HTML content

      // 3. Create the Mapbox Marker
      new mapboxgl.Marker(el)
        .setLngLat(markerInfo.lngLat) // Set marker position
        .setPopup(popup) // Attach the popup
        .addTo(map.value) // Add marker to the map
    })
  })
  // --- End Add Custom Markers ---
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

/*
Style the custom marker element
Since Mapbox adds the marker element outside the component's direct template,
you might need the :deep() selector to pierce the scope boundary if using <style scoped>.
If :deep() doesn't work or you prefer global styles, move this rule
to a non-scoped <style> block or a global CSS file.
*/
:deep(.custom-marker) {
  /* Base styles - size and background image are set dynamically */
  /* background-size: contain; */ /* Alternative */
  background-repeat: no-repeat;
  background-position: center;
  cursor: pointer;
  border-radius: 50%; /* Optional: make it circular */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); /* Optional: add shadow */
}

/* You might need to style the popup if you customize it */
/* :deep(.mapboxgl-popup-content) { ... } */
</style>
