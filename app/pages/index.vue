<template>
    <h1 class="text-3xl">Luke Ferry Tracker</h1>
    <div style="height:100vh; width:100vw; position: relative;">
      <div class="fixed top bottom-6 left-1/2 transform -translate-x-1/2 z-50">
      <NuxtLink 
        href="/report" 
        class="bg-red-600 hover:bg-red-700 text-white font-bold py-4 px-8 rounded-full shadow-lg text-xl transition-colors duration-200 flex items-center gap-3"
      >
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v3m0 0v3m0-3h3m-3 0H9m12 0a9 9 0 11-18 0 9 9 0 0118 0z"></path>
        </svg>
        Report Luke Sighting
      </NuxtLink>
    </div>
    <LMap
      ref="map"
      :zoom="zoom"
      :center="[44.5618, -123.2823]"
      :use-global-leaflet="false"
    >
      <LTileLayer
        url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        attribution="&amp;copy; <a href=&quot;https://www.openstreetmap.org/&quot;>OpenStreetMap</a> contributors"
        layer-type="base"
        name="OpenStreetMap"
      />
    <LCircleMarker
        v-for="sighting in lukeSightings"
        :key="sighting.id"
        :lat-lng="[sighting.latitude, sighting.longitude]"
        :radius="10"
        color="red"
        fill-color="orange"
        fill-opacity="0.5" 
        >
    <LPopup>
        <div>
            <h1>LUKE FERRY SIGHTING</h1>
            <h3>Reported on {{ formatDayAndTime(sighting.created_at) }}</h3>
        </div>
    </LPopup>
    </LCircleMarker>
    </LMap>

  </div>
</template>

<script setup lang="ts">
const zoom = ref(16)
const client = useSupabaseClient()

interface LukeSighting {
  id: number
  created_at: string  // TIMESTAMPTZ from Supabase comes as ISO string
  latitude: number    // NUMERIC(9,7) - precision to ~1cm
  longitude: number   // NUMERIC(10,7) - precision to ~1cm
  // add other fields if needed
}

// Helper function to format timestamp to day and time
function formatDayAndTime(timestamp: string): string {
  const date = new Date(timestamp)
  return date.toLocaleString('en-US', {
    weekday: 'long',  // "Thursday"
    hour: '2-digit',
    minute: '2-digit',
    hour12: true      // 12-hour format with AM/PM
  })
}

const { data: lukeSightings } = await useAsyncData<LukeSighting[]>('luke_sightings', async() => {
    const { data } = await client.from('luke_sightings').select('*')
    
    return data as LukeSighting[]
})


</script>

<style scoped>
.top {
  z-index: 1000 !important;
}
</style>