---
theme: coffee

toc: true
---
# Carte des classes de revenus au fil du temps
<p>Survolez les points avec votre souris pour afficher les informations individuelles.</p>

<!-- Class selector -->
<div class="class-selector">
    <label for="class-select">Afficher la classe de revenu:</label>
    <select id="class-select">
        <option value="all">Toutes</option>
        <option value="0">0–25%</option>
        <option value="1">25–50%</option>
        <option value="2">50–75%</option>
        <option value="3">75–100%</option>
    </select>
</div>

<!-- Map container -->
<div id="map-container" style="height: 500px; margin: 1em 0 2em 0;"></div>
<div class="year-selector">
    <button id="prev-year" class="nav-arrow">‹</button>
    <div id="year-display" class="year-display">1885</div>
    <button id="next-year" class="nav-arrow">›</button>
</div>
<div class="legend">
    <div class="legend-title">Légende - Classes de revenu:</div>
    <div class="legend-item">
        <div class="legend-color" style="background-color: #7E7E7E;"></div>
        <span>0–25% (Classe inférieure)</span>
    </div>
    <div class="legend-item">
        <div class="legend-color" style="background-color: #B36B00;"></div>
        <span>25–50% (Classe moyenne-inférieure)</span>
    </div>
    <div class="legend-item">
        <div class="legend-color" style="background-color: #006400;"></div>
        <span>50–75% (Classe moyenne-supérieure)</span>
    </div>
    <div class="legend-item">
        <div class="legend-color" style="background-color: #D4AF37;"></div>
        <span>75–100% (Classe supérieure)</span>
    </div>
</div>

<style>
.year-selector {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 20px;
    margin-bottom: 20px;
}

.nav-arrow {
    color: white;
    border: none;
    width: 40px;
    height: 40px;
    border-radius: 8px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 18px;
    font-weight: bold;
    transition: all 0.2s ease;
    box-shadow: 0 2px 5px rgba(0,0,0,0.2);
}

.nav-arrow:hover {
    background: #005a8a;
    transform: translateY(-1px);
    box-shadow: 0 3px 8px rgba(0,0,0,0.3);
}

.nav-arrow:active {
    transform: translateY(0);
    box-shadow: 0 1px 3px rgba(0,0,0,0.2);
}

.nav-arrow:disabled {
    visibility: hidden;
}

.year-display {
    background: linear-gradient(135deg,rgb(234, 177, 102) 0%,rgb(162, 107, 75) 100%);
    color: white;
    padding: 15px 30px;
    border-radius: 25px;
    font-size: 24px;
    font-weight: bold;
    text-align: center;
    min-width: 100px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.2);
    text-shadow: 0 1px 2px rgba(0,0,0,0.3);
}

#map-container {
    height: 500px;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 4px 15px rgba(0,0,0,0.1);
}

.legend {
    padding: 15px;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    margin-top: 20px;
}

.legend-title {
    font-weight: bold;
    margin-bottom: 10px;
}

.legend-item {
    display: flex;
    align-items: center;
    margin-bottom: 8px;
}

.legend-color {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    margin-right: 10px;
}
</style>

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
  1923: await FileAttachment('./data/intensity_data_1901.json').json(),
  1951: await FileAttachment('./data/intensity_data_1951.json').json()
};

let yearMapData = {
  1885: await FileAttachment('./data/map_data/map_data_1885.json').json(),
  1901: await FileAttachment('./data/map_data/map_data_1901.json').json(),
  1923: await FileAttachment('./data/map_data/map_data_1923.json').json(),
  1951: await FileAttachment('./data/map_data/map_data_1951.json').json()
}

let currentYearIndex = 0;
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
    data.forEach(([intensity, [lat, lng], job, cat, sector]) => {
        const id = classIdFromIntensity(intensity);
        grouped[id].push([intensity, [lat, lng], job, cat, sector]);
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
        classes[c].forEach(([intensity, [lat, lng], job, cat, sector]) => {
            const marker = L.circle([lat, lng], {
                color: color,
                fillColor: color,
                fillOpacity: 1,
                radius: 3
            });

            // Add hover tooltip
            marker.bindTooltip(
                `<strong>${job}</strong><br>Catégorie: ${cat}<br>${sector}`,
                { permanent: false, direction: "top", opacity: 0.9 }
            );

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

function loadYear(year, selectedClass = "all") {
    clearMarkers();
    classes = processClasses(yearMapData[year]);

    addMarkersToMap(map, selectedClass);
    updateYearDisplay();
    updateNavigationButtons();
}


loadYear(1885);

// Event handlers
document.getElementById("class-select").addEventListener("change", (e) => {
    updateMarkers(e.target.value);
});

document.getElementById('prev-year').addEventListener('click', function() {
    if (currentYearIndex > 0) {
        currentYearIndex--;
        const selectedClass = document.getElementById('class-select').value;
        loadYear(YEARS[currentYearIndex], selectedClass);
    }
});

document.getElementById('next-year').addEventListener('click', function() {
    if (currentYearIndex < YEARS.length - 1) {
        currentYearIndex++;
        const selectedClass = document.getElementById('class-select').value;
        loadYear(YEARS[currentYearIndex], selectedClass);
    }
});

// Keyboard navigation
document.addEventListener('keydown', function(e) {
    if (e.key === 'ArrowLeft' && currentYearIndex > 0) {
        currentYearIndex--;
        const selectedClass = document.getElementById('class-select').value;
        loadYear(currentYearIndex, selectedClass);
    } else if (e.key === 'ArrowRight' && currentYearIndex < YEARS.length - 1) {
        currentYearIndex++;
        const selectedClass = document.getElementById('class-select').value;
        loadYear(currentYearIndex, selectedClass);
    }
});

function updateYearDisplay() {
  document.getElementById('year-display').textContent = YEARS[currentYearIndex];
}

function updateNavigationButtons() {
  const prevBtn = document.getElementById('prev-year');
  const nextBtn = document.getElementById('next-year');

  prevBtn.disabled = currentYearIndex === 0;
  nextBtn.disabled = currentYearIndex === YEARS.length - 1;
}

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
```