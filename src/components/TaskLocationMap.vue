<template>
  <div class="location-picker">
    <form class="location-search" @submit.prevent="searchLocations">
      <input
        v-model="searchQuery"
        type="search"
        placeholder="Pesquisar endereço..."
        aria-label="Pesquisar endereço"
      />
      <button type="submit" :disabled="searching">Pesquisar</button>
    </form>
    <div v-if="searchResults.length" class="search-results">
      <button
        v-for="result in searchResults"
        :key="`${result.latitude}-${result.longitude}`"
        type="button"
        @click="selectLocation(result)"
      >
        {{ result.label }}
      </button>
    </div>
    <p v-if="searchError" class="search-error">{{ searchError }}</p>
    <div ref="mapElement" class="task-location-map" aria-label="Mapa para escolher a localização" />
    <p class="map-help">Clique no mapa ou pesquise um endereço para escolher a localização.</p>
  </div>
</template>

<script setup>
import { nextTick, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import L from 'leaflet'
import geocodingApi from '../api/geocodingApi.js'
import 'leaflet/dist/leaflet.css'
import markerIcon from 'leaflet/dist/images/marker-icon.png'
import markerIcon2x from 'leaflet/dist/images/marker-icon-2x.png'
import markerShadow from 'leaflet/dist/images/marker-shadow.png'

L.Icon.Default.mergeOptions({
  iconRetinaUrl: markerIcon2x,
  iconUrl: markerIcon,
  shadowUrl: markerShadow,
})

const props = defineProps({
  location: { type: Object, default: null },
})
const emit = defineEmits(['location-selected'])

const mapElement = ref(null)
let map
let marker
let accuracyCircle
const searchQuery = ref('')
const searchResults = ref([])
const searching = ref(false)
const searchError = ref('')

function renderLocation() {
  if (!map || !props.location) return
  const point = [props.location.latitude, props.location.longitude]
  map.setView(point, 17)

  marker?.remove()
  accuracyCircle?.remove()
  marker = L.marker(point).addTo(map)
  if (props.location.label) marker.bindPopup(props.location.label).openPopup()

  if (props.location.accuracy > 0) {
    accuracyCircle = L.circle(point, {
      radius: props.location.accuracy,
      color: '#4a90d9',
      fillColor: '#4a90d9',
      fillOpacity: 0.15,
    }).addTo(map)
  }
  nextTick(() => map.invalidateSize())
}

function selectLocation(nextLocation) {
  searchResults.value = []
  searchQuery.value = nextLocation.label
  map?.setView([nextLocation.latitude, nextLocation.longitude], 17)
  emit('location-selected', nextLocation)
}

async function searchLocations() {
  searchError.value = ''
  searchResults.value = []
  if (!searchQuery.value.trim()) return

  searching.value = true
  try {
    searchResults.value = await geocodingApi.search(searchQuery.value)
    if (!searchResults.value.length) searchError.value = 'Nenhum endereço encontrado.'
  } catch {
    searchError.value = 'Não foi possível pesquisar esse endereço.'
  } finally {
    searching.value = false
  }
}

async function handleMapClick(event) {
  searchError.value = ''
  searching.value = true
  try {
    const address = await geocodingApi.reverse(event.latlng.lat, event.latlng.lng)
    selectLocation({
      latitude: event.latlng.lat,
      longitude: event.latlng.lng,
      accuracy: null,
      timestamp: Date.now(),
      label: address?.label ?? 'Localização escolhida no mapa',
    })
  } catch {
    selectLocation({
      latitude: event.latlng.lat,
      longitude: event.latlng.lng,
      accuracy: null,
      timestamp: Date.now(),
      label: 'Localização escolhida no mapa',
    })
  } finally {
    searching.value = false
  }
}

onMounted(() => {
  map = L.map(mapElement.value).setView([0, 0], 2)
  L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution:
      '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
  }).addTo(map)
  map.on('click', handleMapClick)
  renderLocation()
})

watch(() => props.location, renderLocation, { deep: true })
onBeforeUnmount(() => map?.remove())
</script>

<style scoped>
.location-picker {
  position: relative;
  margin-top: 12px;
}

.location-search {
  display: flex;
  gap: 8px;
  margin-bottom: 8px;
}

.location-search input {
  min-width: 0;
  flex: 1;
  padding: 10px 12px;
  border: 1px solid #ccd6e0;
  border-radius: 6px;
  font: inherit;
}

.location-search button,
.search-results button {
  border: 0;
  border-radius: 6px;
  background: #4a90d9;
  color: #fff;
  cursor: pointer;
  font: inherit;
}

.location-search button {
  padding: 0 12px;
}

.location-search button:disabled {
  cursor: wait;
  opacity: 0.6;
}

.search-results {
  position: absolute;
  z-index: 1001;
  top: 48px;
  right: 0;
  left: 0;
  display: grid;
  gap: 4px;
  padding: 6px;
  border: 1px solid #dce3ea;
  border-radius: 6px;
  background: #fff;
  box-shadow: 0 5px 14px rgba(27, 49, 77, 0.14);
}

.search-results button {
  padding: 8px 10px;
  text-align: left;
}

.search-results button:hover {
  background: #357abd;
}

.search-error {
  margin: 6px 0;
  color: #c0392b;
  font-size: 0.85rem;
}

.task-location-map {
  width: 100%;
  height: 240px;
  border-radius: 8px;
  overflow: hidden;
}

.map-help {
  margin-top: 5px;
  color: #718096;
  font-size: 0.78rem;
}
</style>
