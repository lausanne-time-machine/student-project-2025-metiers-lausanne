---
title: Carte soph
toc: false
---


## Carte

<!-- Create the map container -->
<div id="map-container" style="height: 500px; margin: 1em 0 2em 0;"></div>

```js
// Explicit import of leaflet to avoid issues with the Leaflet.heat plugin
import L from "npm:leaflet";

const intensityData = await FileAttachment("./data/intensity_data_with_sector_1951.json").json();

// Helper function to interpolate between blue and red
function getColorFromSector(sector) {
    

    var r = 0      
    var g = 0;                                   
    var b = 0
    if (sector == "Secteur tertiaire") {
        r = 255
    } else if (sector == "Secteur secondaire"){
        g = 255;
    } else if (sector == "Secteur primaire"){
        b = 255;
    }

    return `rgb(${r},${g},${b})`;
}

// Create Map and Layer - Runs Once
function createMapAndLayer(mapContainer) {
    const map = L.map(mapContainer).setView([46.521363371846256, 6.631561628380812], 20);

    const cartoLayer = L.tileLayer("https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}@2x.png", {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
    }).addTo(map);

    intensityData.forEach(([intensity, [lat, lng], job, cat, sector]) => {
        const color = getColorFromSector(sector);

        const circle = L.circle([lat, lng], {
            color: color,
            fillColor: color,
            fillOpacity: 0.5,
            radius: 3
        }).addTo(map);

        // Add hover tooltip
        circle.bindTooltip(
            `<strong>${job}</strong><br>Catégorie: ${cat}<br>Secteur: ${sector}`,
            { permanent: false, direction: "top", opacity: 0.9 }
        );
    });

    return { map };
}

// Call the creation function and store the results
const map = createMapAndLayer("map-container");

```