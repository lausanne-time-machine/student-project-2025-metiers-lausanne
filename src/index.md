---
theme: coffee

toc: true
---

<div class="hero">
  <h1>Cartographie des revenus dans la ville de Lausanne</h1>
  <h2>Une étude de la cartographie des métiers à Lausanne durant le XXe siècle, à partir des annuaires, par le groupe Neuchâtel Fun Machine</h2>

</div>

## Lausanne et l'évolution des métiers au 20e siècle
Lausanne a traversé une transformation majeure de son tissu économique et social au cours du 20e siècle. Au début de cette période, la ville était encore ancrée dans une économie artisanale et industrielle, avec des secteurs comme le textile, le cuir, la construction, la métallurgie et l'alimentation qui jouaient un rôle clé. Les artisans et les ouvriers représentaient une part importante de la population active, surtout en ville, où ils constituaient environ la moitié des chefs de famille.

![](./data/images/carrossiers.png)

*1981, l'Ecole des Métiers de Lausanne forme les carrossiers de demain. Fondation Pierre Izard*

L'industrialisation, qui avait commencé dès le 19e siècle, a pris de l'ampleur avec l'arrivée du chemin de fer à Lausanne en 1856, rendant le transport des marchandises et des personnes beaucoup plus facile. Cette nouvelle infrastructure a permis le développement de zones industrielles le long des voies ferrées, notamment dans l'ouest de la ville. En parallèle, le 20e siècle a vu une transition progressive vers une économie de services à Lausanne. Les secteurs des services, du commerce, de l'administration et des professions libérales ont gagné en importance, modifiant ainsi le paysage professionnel de la ville. Cette évolution a été accompagnée d'une urbanisation croissante et d'une diversification des métiers, reflétant les changements socio-économiques au sein de la société lausannoise.

![](./data/images/vendanges.png)

*Pause de midi lors des vendanges. Les hommes d'un côté les femmes et les enfants de l'autre.*


En lien avec cette évolution des métiers, les modes de rémunération ont également évolué. Les salaires étaient versés de différentes manières : à l'heure, au mois, à la pièce ou à la tâche. Ils pouvaient être complétés par des indemnités pour le travail de nuit, les heures supplémentaires, ou encore par des avantages en nature (logement, nourriture, vêtements), des primes ou des pourboires. Ces différentes formes de rémunération illustrent la diversité des conditions de travail selon les métiers et les secteurs. L'analyse historique révèle aussi de fortes disparités salariales : entre les hommes et les femmes, entre les villes et les campagnes, et entre les différentes branches professionnelles.

![](./data/images/trace_route.png)

*1928, Ouchy. Sous le regard débonnaire d'un gendarme, l'un des premiers engins à tracer la ligne des routes. Fondation Pierre Izard*

Ce projet vise à retracer cette dynamique en proposant des graphiques et une carte interactive illustrant la répartition des métiers à Lausanne au cours du 20e siècle. L’objectif est de mettre en lumière les transformations économiques, sociales et spatiales de la ville à travers ses professions, ses secteurs d’activité et les conditions de vie des travailleurs. Les données de la population sont tirées des annuaires lausannois.


# Quelle est la répartition des métiers et des salaires des habitants et habitantes de Lausanne au XXe siècle?



```js
  const data1901 = [
        { job: "domestique", count: 696 },
        { job: "ménagère", count: 526 },
        { job: "couturière", count: 390 },
        { job: "rentière", count: 296 },
        { job: "manœuvrier", count: 268 },
        { job: "employé", count: 261 },
        { job: "cuisinière", count: 254 },
        { job: "menuisier", count: 251 },
        { job: "maçon", count: 226 },
        { job: "agriculteur", count: 224 }
  ]
  const data1951 = [
        { job: "manœuvrier", count: 556 },
        { job: "représentant", count: 508 },
        { job: "vendeuse", count: 504 },
        { job: "étudiant", count: 452 },
        { job: "maçon", count: 433 },
        { job: "peintre", count: 426 },
        { job: "chauffeur", count: 405 },
        { job: "menuisier", count: 385 },
        { job: "comptable", count: 343 }
  ]
```

<div class="grid grid-cols-2" style="grid-auto-rows: 504px;">
  <div class="card" id="plot-1901">${
    resize((width) =>
      Plot.plot({
        title: "Top 10 des métiers en 1901",
        width,
        height: 480,
        marginLeft: 100,
        x: { label: "Nombre", labelOffset: 30  },
        y: { label: "Métier", domain: data1901.map(d => d.job), padding: 0.1 },
        marks: [Plot.barX(data1901, { x: "count", y: "job", tip: true })],
        style: {
          fontSize: "12px"
        }
})
    )
  }</div>

  <div class="card" id="plot-1951">${
    resize((width) =>
      Plot.plot({
        title: "Top 10 des métiers en 1951",
        width,
        height: 480,
        marginLeft:100,
        x: { label: "Nombre", labelOffset: 30 },
        y: { label: "Métier", domain: data1951.map(d => d.job), padding: 0.1 },
        marks: [Plot.barX(data1951, { x: "count", y: "job", tip: true })],
        style: {
          fontSize: "12px"
        }
      })
    )
  }</div>
</div>

---
```js

const avg = FileAttachment("data/plot_data/sector_avg.json").json()
```

<div class="card" id="plot-sector-trends">${
  resize((width) =>
    Plot.plot({
      title: "Évolution des salaires moyens par secteur",
      width,
      height: 480,
      marginLeft: 60,
      marginBottom: 50,
      x: { label: "Annee", type: "linear" },
      y: { label: "Salaire moyen", grid: true },
      color: { legend: true, label: "Secteur", scheme: "category10" },
      marks: [
        Plot.line(avg, {
          x: "Annee",
          y: "salary",
          stroke: "sector"
        }),
        Plot.dot(avg, {
          x: "Annee",
          y: "salary",
          stroke: "sector",
          tip: true
        })
      ],
      style: {
        fontSize: "12px"
      }
    })
  )
}</div>





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
  1923: await FileAttachment('./data/intensity_data_1901.json').json(),
  1951: await FileAttachment('./data/intensity_data_1951.json').json()
};

let yearMapData = {
  1885: await FileAttachment('./data/map_data/map_data_1885.json').json(),
  1901: await FileAttachment('./data/map_data/map_data_1901.json').json(),
  1923: await FileAttachment('./data/map_data/map_data_1923.json').json(),
  1951: await FileAttachment('./data/map_data/map_data_1951.json').json()
}

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

// Initial load
function loadYear(year, selectedClass = "all") {
    clearMarkers();
    classes = processClasses(yearMapData[year]);
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
```

# Analyses
sign

# Sources
[Wikipedia](https://fr.wikipedia.org/wiki/Lausanne)
[Dictionnaire historique de la Suisse](https://hls-dhs-dss.ch/fr/articles/002408/2009-04-02/)
[Le tissu économique de la région lausannoise](https://www.lausanne.ch/.binaryData/website/path/lausanne/officiel/statistique/publications/apercus-par-statistique-vaud/contentAutogenerated/autogeneratedContainer/col1/00/linkList/00/websitedownload/CS-06-2007_Evolution-de-l-emploi.2018-01-31-16-21-38.pdfm)

<style>

.hero {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: var(--sans-serif);
  margin: 4rem 0 8rem;
  text-wrap: balance;
  text-align: center;
}

.hero h1 {
  margin: 1rem 0;
  padding: 1rem 0;
  max-width: none;
  font-size: 14vw;
  font-weight: 900;
  line-height: 1;
  background: linear-gradient(30deg, var(--theme-foreground-focus), currentColor);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.hero h2 {
  margin: 0;
  max-width: 34em;
  font-size: 20px;
  font-style: initial;
  font-weight: 500;
  line-height: 1.5;
  color: var(--theme-foreground-muted);
}

@media (min-width: 640px) {
  .hero h1 {
    font-size: 90px;
  }
}
</style>
