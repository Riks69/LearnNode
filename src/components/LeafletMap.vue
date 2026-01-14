<script setup>
//import * as L from 'leaflet';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import 'leaflet/dist/images/marker-icon-2x.png';
import 'leaflet/dist/images/marker-icon.png';
import 'leaflet/dist/images/marker-shadow.png';
import { onMounted, useId, watch } from 'vue';

const {center, zoom} = defineProps(['center', 'zoom']);
const id = 'map-' + useId();
let map;
onMounted(() => {
    map = L.map(id).setView(center, zoom);
    L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
        maxZoom: 19,
        attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
    }).addTo(map);
    var marker = L.marker([59.4071715, 24.771323]).addTo(map);

    var polygon = L.polygon([
    [59.4071715, 24.771323],
    [59.382572, 24.769856],
    [59.382528, 24.754599],
    [59.385315, 24.748377],
    [59.402751, 24.742218],
    [59.412952, 24.771348],
    [59.407179, 24.789163],


  ]).addTo(map);

});
watch(() => center, (center, oldCenter) => {
    console.log(center, oldCenter);
    map.panTo(center);
});

watch(() => zoom, (zoom) => {
    map.setZoom(zoom);
});
</script>
<template>
     <div :id="id"></div>
</template>
<style scoped>
div { 
    height: 90vh;
}
</style>