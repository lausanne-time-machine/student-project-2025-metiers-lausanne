---
theme: coffee
title: Méthodologie
toc: false
---


# Méthodologie

Cette page présente les sources des données utilisées et la méthodologie appliquée.

## Traitement des données salariales

- Les données proviennent du site statistique historique suisse [HSSO.ch](https://hsso.ch/).
- Elles recensent 23 catégories de métiers, réparties entre les secteurs primaire, secondaire et tertiaire.
- Un indice salarial couvrant le XIXe siècle permet d’estimer les salaires approximatifs pour chaque catégorie, selon les années.
- Grâce aux indicateurs vaudois de la Time Machine Unit, nous avons aussi obtenu des informations sur les noms, métiers et adresses des habitants.

|       |                       |      |                   |                                |     |Secteur ternaire et secondaire | | | | | | |
| ----- | --------------------- | ---------------------------------- | ----------------- | ------------------------------ |---  |-|-|-|-|-|-|-|
| Année | Produits alimentaires | Vêtem., lingerie, chauss., literie | Industrie textile | Industrie du papier            | Arts graphiques | Industrie chimique | Bois, liège, meubles | Pierre et terre | Industrie des métaux | Horlogerie | Bijouterie, gravure, frappe |
| 1968 | 1966 | 1864 | 1892 | 2026 | 1932 | 2178 | 1792 | 1946 | 1887 | 1875 | 1996 |
| 1969 | 2059 | 1960 | 2016 | 2144 | 2035 | 2376 | 1883 | 2056 | 1981 | 1988 | 2076 |
| 1970 | 2179 | 2103 | 2165 | 2295 | 2152 | 2510 | 2033 | 2233 | 2146 | 2081 | 2124 |
| 1971 | 2390 | 2297 | 2359 | 2529 | 2426 | 2891 | 2220 | 2471 | 2344 | 2262 | 2287 |
| 1972 | 2616 | 2488 | 2547 | 2733 | 2632 | 3148 | 2496 | 2711 | 2599 | 2422 | 2515 |
| 1973 | 2943 | 2799 | 2845 | 3031 | 2965 | 3378 | 2803 | 3031 | 2910 | 2641 | 2820 |

**Table 1** – Extrait des données représentant le salaire mensuel moyen par secteur et par année 
## Catégorisation des métiers

- Les métiers ont été classés dans les catégories correspondantes pour pouvoir leur attribuer un salaire estimé.
- Cette étape a été réalisée avec [Llama3.3-instruct](https://huggingface.co/meta-llama/Llama-3.3-70B-Instruct).


## Géolocalisation des habitants et habitantes

- Pour géolocaliser les personnes, nous avons utilisé l’outil [Nominatim](https://nominatim.org/).
