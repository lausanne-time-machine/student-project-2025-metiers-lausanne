---
title: Cadastre Rénové
toc: false
---

# Cadastre Rénové
Cette page présente le cadastre rénové de Lausanne (1889).

## Carte

<!-- Create the map container -->
<div id="map-container" style="height: 500px; margin: 1em 0 2em 0;"></div>

```js
// Explicit import of leaflet to avoid issues with the Leaflet.heat plugin
import L from "npm:leaflet";

const intensityData = await FileAttachment("./data/intensity_data_1951.json").json();

let classes = {
    0: [],
    1: [],
    2: [],
    3: []
}

const NBR_CLASSES = 4;

function clamp(num, min, max) {
    return Math.min(Math.max(num, min), max);
} 

intensityData.forEach(([intensity, [lat, lng]]) => {
    const id = Math.floor(clamp(NBR_CLASSES * intensity, 0, NBR_CLASSES - 0.001))
    classes[id].push((intensity, [lat, lng]));
});

const colors = ['#22a2e2','#6be222', '#6be222', '#EF0E0E'];

// Helper function to interpolate between blue and red
function getColorFromClass(id) {
    return colors[id];
}

// Create Map and Layer - Runs Once
function createMapAndLayer(mapContainer) {
    const map = L.map(mapContainer).setView([46.521363371846256, 6.631561628380812], 20);

    const cartoLayer = L.tileLayer("https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}@2x.png", {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
    }).addTo(map);

    for (let c in classes) {
        const color = getColorFromClass(c);
        classes[c].forEach(([lat, lng]) => {
            L.circle([lat, lng], {
                color: color,
                fillColor: color,
                fillOpacity: 0.3,
                radius: 3
            }).addTo(map);
        });
    }

    return { map };
}

// Call the creation function and store the results
const map = createMapAndLayer("map-container");

```