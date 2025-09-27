<template>
    <h1 class="text-3xl text-center">REPORT LUKE FERRY SIGHTINGS HERE</h1>
    <p>Click anywhere on Map</p>
    <div class="z-0" style="height: 100vh; width: 100vw;">
        <button @click="submitReport" v-if="pinDown" class="submit-btn bg-blue-500 text-white rounded-md p-4 absolute bottom-32 left-0 right-0 mx-auto w-32">SUBMIT REPORT</button>
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
        }
    })
}


</script>

<style scoped>
.submit-btn {
    z-index: 1000 !important;
}

.submit-btn:hover {
    background-color: #2563eb !important;
}
</style>