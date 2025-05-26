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

// Wait for L to be defined before importing the Leaflet.heat plugin
// This is necessary because Leaflet.heat depends on the L variable being defined
if (L === undefined) console.error("L is undefined");

// Leaflet.heat: https://github.com/Leaflet/Leaflet.heat/
import "./plugins/leaflet-heat.js";
if (!L.heatLayer) console.error("Leaflet.heat plugin failed to register `L.heatLayer`.");
console.log(L.heatLayer)

const intensityData = await FileAttachment("./data/salaries_data_1885.json").json();

// Create Map and Layer - Runs Once
function createMapAndLayer(mapContainer) {
    const map = L.map(mapContainer).setView([46.521363371846256, 6.631561628380812], 20);

    const osmLayer = L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
    });

    const cartoLayer = L.tileLayer("https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}@2x.png", {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
    }).addTo(map);

    // Crate a control to switch between layers
    const layerControl = L.control.layers().addTo(map);

    // Add the OSM and Carto layers to the control
    layerControl.addBaseLayer(osmLayer, "OSM");
    layerControl.addBaseLayer(cartoLayer, "Carto");

    // Return the the map instance, the layer group, and the mapping
    return { map, layerControl };
}

// Call the creation function and store the results
const mapElements = createMapAndLayer("map-container");

// Build heatmap data from intensityData (which is [intensity, [lat, lng]])
const heatmapData = intensityData.map(([intensity, [lat, lng]]) => {
    return [lat, lng, intensity]; // format expected by Leaflet.heat
});

const heatmapLayer = L.heatLayer(heatmapData, {
    radius: 10,
    blur: 15,
});

heatmapLayer.addTo(mapElements.map); // optional: show it by default

// // Add the heatmap layer to the layer control
mapElements.layerControl.addOverlay(heatmapLayer, "Heatmap");
```

## Registre

```js
const registre = FileAttachment("./data/lausanne-1888-cadastre-renove-registre-20250410.csv").csv()
```

```js
// Create a list of column names
const columnNames = Object.keys(registre[0]);
```

```js
// Create a Map of the merge_id to corresponding entries in the registre
// This is used to populate the popups with the relevant data for each point
const mergeIDMap = new Map();
registre.forEach(entry => {
    const merge_id = entry.merge_id;
    if (!mergeIDMap.has(merge_id)) {
        mergeIDMap.set(merge_id, []);
    }
    mergeIDMap.get(merge_id).push(entry);
});
```

_Utilisez le champ de recherche pour filtrer les entrées du registre. Les points affichés sur la carte sont mis à jour en fonction du résultat de la recherche._

```js
const searchResults = view(Inputs.search(registre))
```

```js
Inputs.table(searchResults)
```

```js
const filteredMergeIDs = searchResults.map(r => r.merge_id)
```

```js
const filteredMergeIDsSet = new Set(filteredMergeIDs);
```
