# DVF Script 4 — Gironde / Bordeaux Métropole (RMarkdown)

Script **RMarkdown** pour analyser les données **DVF** et produire des **indicateurs + cartes** à l’échelle de **Bordeaux Métropole** (IRIS, sections cadastrales, mutations) et un zoom sur **Bordeaux** (évolution 2014–2022 des prix au m² des appartements).

**Auteurs** : Boris Mericskay & Florent Demoraes (script initial) — adaptations : Vitaliana Brasset, Saïd Khatir, Killian Mouton  
**Dates** : 02/07/2021 – MAJ 15/10/2023 – Adapté le 08/11/2023

---

## Ce que fait le script
- Importe `DVFOK.csv`, crée une couche **sf** à partir de `longitude/latitude`, reprojette en **EPSG:2154**.
- Exporte les mutations en **GeoPackage** : `MutationsGIRONDE.gpkg`.
- Filtre **Bordeaux Métropole** (`EPCI == "Bordeaux Métropole"`) et calcule un récap par `type` (prix, surface, prix/m²).
- Cartographie :
  - **Prix moyen au m²** par **IRIS** (choroplèthe)
  - **Nombre de mutations** par IRIS (symboles proportionnels)
  - Prix moyen au m² par **section cadastrale** + nb mutations
- Analyses spatiales :
  - distances au plus proche voisin
  - **Moran’s I** (autocorrélation)
  - **lissages** des prix (IRIS / sections / mutations) via `spatstat::Smooth` + raster `terra`
- Typologie des sous-marchés (IRIS) :
  - indicateurs par IRIS → **CAH Ward** (`cluster::agnes`) → **5 classes**
  - export : `IRISDVFCAH.gpkg`
- Zoom Bordeaux : série de **cartes lissées annuelles** des prix/m² des appartements (2014–2022).

---

## Données attendues
- `DVFOK.csv` (au minimum : `longitude`, `latitude`, `EPCI`, `type`, `prix`, `surface`, `prixm2`, `annee`, `id`)
- `IRISBM_ok.shp` (champ `code_iris`)
- `CommuneGironde.shp` (champ `NOM_COM`)
- `Sectionscadastrales_ok.shp` (champ `id`)

---

## Installation
```r
install.packages(c("tidyverse","sf","cartography","spdep","geoR","spatstat","terra","cluster"))
