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

> *À faire : ajouter un exemple ou une capture d’écran des données issues de HSSO.ch? *

## Catégorisation des métiers

- Les métiers ont été classés dans les catégories correspondantes pour pouvoir leur attribuer un salaire estimé.
- Cette étape a été réalisée avec [Llama3.3-instruct](https://huggingface.co/meta-llama/Llama-3.3-70B-Instruct).

**À ce stade, une interface interactive permet de choisir un métier et une année pour voir le salaire approximatif associé.**

> *Remarque : Claude y arrive mais ça fonctionne pas ici*

## Géolocalisation des habitants et habitantes

- Pour géolocaliser les personnes, nous avons utilisé l’outil [Nominatim](https://nominatim.org/).
