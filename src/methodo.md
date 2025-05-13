---
theme: coffee 
title: Méthodologie
toc: false
---

# Méthodologie

Sur cette page se trouvent les sources de nos données et notre méthodologie.

## Traitement des données salariales

TODO : trouvées sur HSSO.ch, montrer exemple, avec catégories

Nous avons utilisé les données présentes sur le site statistique historique de la suisse [HSSO.ch](https://hsso.ch/).

Ces données répértorient 23 catégories de métiers, réparties entre les secteurs primaire, secondaire et tertiaire. Les données comprennent également un indice salarial couvrant le XIXe siècle, ce qui nous a permis d’estimer les salaires approximatifs associés à chacune des catégories au fil du temps.

Grâce aux indicateurs vaudois mis à disposition par la Time Machine Unit, nous avons obtenu des informations précieuses : noms des habitants, métiers exercés et adresses. 

## Catégorisation des métiers

Nous avons ensuite classé les métiers dans les catégories correspondantes afin de pouvoir leur attribuer un salaire estimé. Cette étape a été réalisée à l’aide de [Llama3.3-instruct](https://huggingface.co/meta-llama/Llama-3.2-1B).

Voici une interface interactive qui permet de choisir un métier et une année et de voir quel était le salaire approximatif.


## Géolocalisation des habitants et habitantes sur la carte

Pour géolocaliser les personnes, nous avons utilisé l’outil [Nominatim](https://nominatim.org/).