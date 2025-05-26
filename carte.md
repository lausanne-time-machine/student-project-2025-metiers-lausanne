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

// Helper function to interpolate between blue and red
function getColorFromIntensity(intensity) {
    // Clamp intensity between 0 and 1
    intensity = Math.max(0, Math.min(1, intensity));

    const r = Math.round(255 * intensity);        // Red increases with intensity
    const g = 0;                                   // Green stays at 0
    const b = Math.round(255 * (1 - intensity));   // Blue decreases with intensity

    return `rgb(${r},${g},${b})`;
}

// Create Map and Layer - Runs Once
function createMapAndLayer(mapContainer) {
    const map = L.map(mapContainer).setView([46.521363371846256, 6.631561628380812], 20);

    const cartoLayer = L.tileLayer("https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}@2x.png", {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
    }).addTo(map);

    intensityData.forEach(([intensity, [lat, lng]]) => {
        const color = getColorFromIntensity(intensity);

        L.circle([lat, lng], {
            color: color,
            fillColor: color,
            fillOpacity: 0.5,
            radius: 3
        }).addTo(map);
    });

    return { map };
}

// Call the creation function and store the results
const map = createMapAndLayer("map-container");

```