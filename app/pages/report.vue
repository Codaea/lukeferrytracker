<template>
    <h1 class="fixed top top-4 left-1/2 transform -translate-x-1/2 text-2xl text-center bg-red-600 text-white p-4 w-4/5 rounded-xl shadow-lg">
        CLICK ANYWHERE TO REPORT LUKE FERRY SIGHTING
    </h1>
    <div class="z-0" style="height: 100vh; width: 100vw;">
        <div class="fixed top bottom-6 left-1/2 transform -translate-x-1/2 z-50">
            <button @click="submitReport" v-if="pinDown" class="bg-red-600 hover:bg-red-700 text-white font-bold py-4 px-8 rounded-full shadow-lg text-xl transition-colors duration-200 flex items-center gap-3">
                Report Luke Sighting
            </button>
        </div>
        <LMap @click="onMapClick" ref="map" :zoom="zoom" :center="[44.5618, -123.2823]" :use-global-leaflet="false">
            <LTileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
            attribution="&amp;copy; <a href=&quot;https://www.openstreetmap.org/&quot;>OpenStreetMap</a> contributors"
            layer-type="base" name="OpenStreetMap" />
            
            
            <LMarker :lat-lng="lat_lng"> </LMarker>
        </LMap>
    </div>
</template>

<script setup lang="ts">
const zoom = ref(16)
const lat_lng = ref([])
const pinDown = ref(false)

const client = useSupabaseClient()
const router = useRouter()

function onMapClick(e: L.LeafletMouseEvent) {
    const { lat, lng } = e.latlng
    lat_lng.value = e.latlng
    pinDown.value = true
    // Handle the click event, e.g., store the coordinates or display a marker
    console.log(`Map clicked at latitude: ${lat}, longitude: ${lng}`)
}

function submitReport() {
    // Logic to submit the report to Supabase
    if (lat_lng.value.length === 0) {
        alert("Please select a location on the map first.")
        return
    }

    const { lat, lng } = lat_lng.value

    client.from('luke_sightings').insert([
        { latitude: lat, longitude: lng }
    ]).then(({ data, error }) => {
        if (error) {
            console.error("Error submitting report:", error)
            alert("There was an error submitting your report. Please try again.")
        } else {
            alert("Report submitted successfully!")
            // Reset the state
            lat_lng.value = []
            pinDown.value = false
            router.push('/')
        }
    })
}


</script>

<style scoped>
.top {
    z-index: 1000 !important;
}
</style>