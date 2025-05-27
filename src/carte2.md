---
theme: coffee

toc: true
---
# Carte des domaines au fil du temps
<p>Survolez les points avec votre souris pour afficher les informations individuelles.</p>

<!-- Class selector -->
<div class="class-selector">
  <label for="class-select">Afficher le métier :</label>
  <select id="class-select">
    <option value="all">Tous les métiers</option>
    <option value="0">Produits alimentaires</option>
    <option value="1">Vêtem., lingerie, chauss., literie</option>
    <option value="2">Industrie textile</option>
    <option value="3">Industrie du papier</option>
    <option value="4">Arts graphiques</option>
    <option value="5">Industrie chimique</option>
    <option value="6">Bois, liège, meubles</option>
    <option value="7">Pierre et terre</option>
    <option value="8">Industrie d. métaux(2)</option>
    <option value="9">Horlogerie</option>
    <option value="10">Bijouterie, gravure, frappe</option>
    <option value="11">Construct., charpenterie</option>
    <option value="12">Gaz, eau, éléctricité</option>
    <option value="13">Commerce de gros</option>
    <option value="14">Commerce de détail</option>
    <option value="15">Banques, établissments financ. </option>
    <option value="16">Assurances privées</option>
    <option value="17">Agences, location, consultation</option>
    <option value="18">Affaires immobilières, location </option>
    <option value="19">Bureaux de consultation</option>
    <option value="20">Hôtellerie, restaurants</option>
    <option value="21">Transports</option>
    <option value="22">Administration publique</option>
    <option value="23">Réparations</option>
    <option value="24">Vachers célibataires</option>
    <option value="25">Employés pour tous travaux, célibataire</option>
    <option value="26">Employées pour le ménage et la ferme</option>
    <option value="27">Journaliers; dans la salaire y compris l'entretien</option>
    <option value="28">Journalières dans la salaire y compris l'entretien</option>
  </select>
</div>


```js
const jobToColor = await FileAttachment('./data/map_data/cat_to_color.json').json()
const CATEGORY_NUMBER = Object.keys(jobToColor).length;

```


<!-- Map container -->
<div id="map-container" style="height: 500px; margin: 1em 0 2em 0;"></div>
<div class="year-selector">
    <button id="prev-year" class="nav-arrow">‹</button>
    <div id="year-display" class="year-display">1885</div>
    <button id="next-year" class="nav-arrow">›</button>
</div>
<div class="legend">
  <div class="legend-title">Légende - Secteurs d'activité:</div>
  <div id="legend-items"></div>
</div>

```js
const legendContainer = document.getElementById("legend-items");

for (const job in jobToColor) {
  const color = jobToColor[job];

  const item = document.createElement("div");
  item.className = "legend-item";

  const colorBox = document.createElement("div");
  colorBox.className = "legend-color";
  colorBox.style.backgroundColor = color;

  const label = document.createElement("span");
  label.textContent = job;

  item.appendChild(colorBox);
  item.appendChild(label);
  legendContainer.appendChild(item);
}
```

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
    width: 50%;
    align-items: center;
    margin-bottom: 8px;
}

.legend-color {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    margin-right: 10px;
}
#legend-items {
    display: flex;         /* ✅ Enable flex layout */
    flex-wrap: wrap;       /* ✅ Allow wrapping to form columns */
}
</style>

```js
import L from "npm:leaflet";

const YEARS = [1885, 1901, 1923, 1951];
const colors2 = ['#7E7E7E', '#B36B00', '#006400', '#D4AF37'];


const NBR_CLASSES = 29;
const mapCenter = [46.521363371846256, 6.631561628380812];
const zoomLevel = 14;

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

function classIdFromCategory(category) {
    return Object.keys(jobToColor).indexOf(category)
}

function getEmptyClasses(number) {
    let grouped = {}
    for (let i = 0; i < number; i+=1) {
        grouped[i] = []
    }
    return grouped;
}

function processClasses(data) {
    const grouped = getEmptyClasses(CATEGORY_NUMBER);
    data.forEach(([intensity, [lat, lng], job, cat, sector]) => {
        const id = classIdFromCategory(cat);
        console.log(cat);
        console.log(id);
        if (id != -1) {
            grouped[id].push([intensity, [lat, lng], job, cat, sector]);
        }
    });
    return grouped;
}

function clearMarkers() {
    for (let c in markers) {
        markers[c].forEach(m => m.remove());
    }
    markers = getEmptyClasses(CATEGORY_NUMBER);
}

function addMarkersToMap(map, selectedClass = "all") {
    for (let c in classes) {
        
        classes[c].forEach(([intensity, [lat, lng], job, cat, sector]) => {
            const color = jobToColor[cat]
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