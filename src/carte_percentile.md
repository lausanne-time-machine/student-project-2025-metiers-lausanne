---
title: Cadastre Rénové
toc: false
---

# Cadastre Rénové
Cette page présente le cadastre rénové de Lausanne.

## Carte

<!-- Year slider -->
<label for="year-slider">Année:</label>
<input type="range" id="year-slider" min="0" max="3" step="1" value="0" />
<span id="year-label">1885</span>

<!-- Class selector -->
<label for="class-select">Afficher la classe de revenu:</label>
<select id="class-select">
  <option value="all">Toutes</option>
  <option value="0">0–25%</option>
  <option value="1">25–50%</option>
  <option value="2">50–75%</option>
  <option value="3">75–100%</option>
</select>

<!-- Map container -->
<div id="map-container" style="height: 500px; margin: 1em 0 2em 0;"></div>

```js
import L from "npm:leaflet";

const YEARS = [1885, 1901, 1923, 1951];

// Configuration
const YEAR_FILES = {
  1885: './data/intensity_data_1885.json',
  1901: './data/intensity_data_1901.json',
  1951: './data/intensity_data_1951.json'
};

const NBR_CLASSES = 4;
const colors2 = ['#7E7E7E', '#B36B00', '#006400', '#D4AF37'];
const mapCenter = [46.521363371846256, 6.631561628380812];
const zoomLevel = 14;

let yearData = {
  1885: await FileAttachment('./data/intensity_data_1885.json').json(),
  1901: await FileAttachment('./data/intensity_data_1901.json').json(),
  1951: await FileAttachment('./data/intensity_data_1951.json').json()
};              // Loaded intensity data for each year
let classes = {};              // Currently visible classes
let markers = {};              // Currently drawn markers

// Helpers
function clamp(num, min, max) {
    return Math.min(Math.max(num, min), max);
}

function classIdFromIntensity(intensity) {
    return Math.floor(clamp(NBR_CLASSES * intensity, 0, NBR_CLASSES - 0.001));
}

function processClasses(data) {
    const grouped = { 0: [], 1: [], 2: [], 3: [] };
    data.forEach(([intensity, [lat, lng]]) => {
        const id = classIdFromIntensity(intensity);
        grouped[id].push([lat, lng]);
    });
    return grouped;
}

function clearMarkers() {
    for (let c in markers) {
        markers[c].forEach(m => m.remove());
    }
    markers = { 0: [], 1: [], 2: [], 3: [] };
}

function addMarkersToMap(map, selectedClass = "all") {
    for (let c in classes) {
        const color = colors2[c];
        classes[c].forEach(([lat, lng]) => {
            const marker = L.circle([lat, lng], {
                color: color,
                fillColor: color,
                fillOpacity: 1,
                radius: 3
            });
            if (selectedClass === "all" || selectedClass == c) {
                marker.addTo(map);
            }
            markers[c].push(marker);
        });
    }
}

// Map setup
const map = L.map("map-container").setView(mapCenter, zoomLevel);
L.tileLayer("https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}@2x.png", {
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
}).addTo(map);

// Initial load
function loadYear(year, selectedClass = "all") {
    clearMarkers();
    classes = processClasses(yearData[year]);
    addMarkersToMap(map, selectedClass);
}

loadYear(1901);

// Event handlers
document.getElementById("class-select").addEventListener("change", (e) => {
    updateMarkers(e.target.value);
});

document.getElementById("year-slider").addEventListener("input", (e) => {
  const index = parseInt(e.target.value);
  const year = YEARS[index];
  document.getElementById("year-label").textContent = year;
  const selectedClass = document.getElementById("class-select").value;
  loadYear(year, selectedClass);
});

function updateMarkers(selectedClass) {
    for (let c in markers) {
        markers[c].forEach(marker => {
            if (selectedClass === 'all' || selectedClass == c) {
                marker.addTo(map);
            } else {
                marker.remove();
            }
        });
    }
}
