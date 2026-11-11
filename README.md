# Prairie Vigie

Observatoire des conversions présumées de prairies permanentes en cultures en Normandie (campagnes 2019 à 2025),
et carte des secteurs où agir en priorité avant la campagne 2027.
Projet réalisé pour le Datathon Normandie 2026, « Biodiversité et activité humaine en Normandie ».

Auteures : Sara CHEKLAT et Mariem GAALICHE, Master 2 « Apprentissage automatique pour l'IA », Université de Rouen Normandie.

## En bref

- **Mesure** : chaque année, les parcelles du Registre parcellaire graphique (RPG) sont superposées à celles de l'année
  suivante. Une prairie permanente dont plus de la moitié est déclarée en terre arable l'année suivante est une
  **conversion présumée** (déclarations PAC, sans constat de terrain).
- **Résultat principal** : environ 49 700 ha de conversions présumées entre 2019 et 2025, pour environ 4 630 ha de prairies
  recréées. Les totaux de prairies permanentes s'écartent de +0,5 % (2018) et +0,4 % (2025) des surfaces officielles.
- **Modèle** : régression logistique, prédiction à 2 ans (le RPG d'une année n'est publié que l'année suivante).
  Pour classer les territoires, l'historique observé fait mieux que le modèle : la carte publique montre donc l'historique.
  Pour cibler les prairies à l'intérieur d'un territoire, le modèle retrouve 25,8 % des conversions dans les 10 % de prairies
  signalées, contre 17,3 % pour le meilleur historique local (test 5 ter).
- **Priorités** : prairies dans les 10 % les plus à risque et en zone humide, aire d'alimentation de captage ou habitat Natura 2000 :
  6 488 prairies, 12 354 ha (niveau 1), dont 2 885 prairies, 6 466 ha avec au moins deux enjeux (niveau 2), soit environ 2 % des prairies.

## Utiliser les résultats sans rien exécuter

- **La carte** : ouvrir `carte/prairie_vigie.html` dans un navigateur, ou en ligne : https://sarayaouk.github.io/prairie_vigie/carte/prairie_vigie.html
- **Les chiffres** : les fichiers CSV de `donnees/`, lisibles dans un tableur ; les définitions sont dans
  `donnees/DICTIONNAIRE_DES_DONNEES.md`, et la rubrique « Où lire les principaux chiffres » ci-dessous indique où trouver chaque résultat.
- **Les cartes SIG** : `donnees/communes.geojson`, `intercos.geojson` et `mailles10km.geojson` s'ouvrent dans QGIS.

Relancer les notebooks n'est utile que pour reproduire les calculs ou les mettre à jour avec un nouveau millésime du RPG.

## Contenu du dépôt

| Dossier ou fichier | Contenu |
|---|---|
| `notebooks/` | Les 4 notebooks, à lancer dans l'ordre (voir ci-dessous) |
| `carte/prairie_vigie.html` | La carte web, page autonome (ouvrir dans un navigateur) |
| `donnees/` | Les résultats agrégés diffusables (contenu de `publiable.zip`, produit par le notebook 4) |
| `figures/` | Calibration et courbes de dépendance du modèle |
| `donnees/DICTIONNAIRE_DES_DONNEES.md` | Définitions et description de chaque fichier |
| `requirements.txt` | Bibliothèques Python |
| `LICENSE`, `LICENSE-DONNEES.md` | Licences du code et des données |

## Exécuter

Les notebooks sont prévus pour **Google Colab**. Ils travaillent tous dans le même dossier de « Mon Drive »,
défini par la variable `DOSSIER` de leur première cellule (`datathon_prairies` par défaut). Hors Colab, ils travaillent
dans le dossier courant (`pip install -r requirements.txt`, puis Jupyter). Chaque notebook réutilise ce que le précédent
a téléchargé ou calculé et ne refait jamais un travail terminé : après une coupure, il suffit de relancer « Tout exécuter ».

| Ordre | Notebook | Rôle | Durée |
|---|---|---|---|
| 1 | `1_mesure_2015_2023_v3.ipynb` | Télécharge le RPG 2015-2023 et les communes, mesure les conversions présumées 2019→2023, valide un premier modèle (règles de décision écrites avant les résultats) | environ 1 h 20 la première fois (76 min de téléchargement + 6 min de calcul) |
| 2 | `2_mesure_2024_2025_v4.ipynb` | Ajoute 2024 et 2025, mesure 2023→2025, vérifie la prédiction sur des données jamais vues | environ 35 min la première fois (26 min de téléchargement + 8 min de calcul) |
| 3 | `3_consolidation_v5.ipynb` | Qualité des conversions, choix du modèle, calibration, campagne 2023→24, hectares attendus en 2027, explicabilité, énergie consommée | environ 6 min, données en cache (mesurée avec CodeCarbon) |
| 4 | `4_prioriser_et_carte.ipynb` | Enjeux écologiques, priorités, tests de l'apport du modèle (5 bis, 5 ter), données de la carte, dossier publiable contrôlé | environ 20 min la première fois, surtout les haies (15 min) ; `AVEC_HAIES = False` pour aller plus vite |

Reproductibilité : graines aléatoires fixées (`random_state=0`, `default_rng(0)`). Les versions exactes des bibliothèques
sont écrites par le notebook 3 dans `versions_bibliotheques.txt`. Les données sources peuvent être republiées ou mises à jour
par leurs producteurs : de petits écarts sont alors possibles.

Sobriété : le notebook 3 mesure sa propre consommation avec CodeCarbon (`donnees/sobriete_v5.csv` : 6 min, 0,001 kWh,
0,2 g CO2e lors de notre exécution du 11/11/2026, données en cache). Ce chiffre ne couvre pas les téléchargements des notebooks 1 et 2.

## Où lire les principaux chiffres

| Résultat | Fichier (`donnees/`) | Lecture |
|---|---|---|
| Prairies permanentes 2018 et 2025 | `surfaces_par_dep_annee_2015_2025.csv` | somme de `pp` par `annee` |
| Conversions présumées et créations par campagne | `bilan_transitions_2019_2025.csv` | somme de `ha_retournees` et `ha_gains` par `transition` |
| Sorties du RPG | même fichier | `n_sorties_rpg` : un **nombre de prairies**, pas des hectares |
| Persistance à 2 ans | `qualite_etiquettes.csv` | moyenne de `encore_cultivees_N+2` pondérée par `conversions` |
| Performance du modèle à 2 ans | `evaluation_complete.csv` | `test = "2 ans, 2023 -> 2024->25"`, `zone = "Normandie"`, colonne `lift_10` |
| Modèle contre historique | `valeur_ajoutee_modele_vs_historique.csv` (territoires), `valeur_ajoutee_parcelle.csv` (prairies) | colonnes `modele`/`historique`, `rappel_top10` |
| Priorités niveaux 1 et 2 | `priorites_synthese_normandie.csv` | `n_prairies`, `ha` |
| Campagne 2023→24 | `effet_regime_2023_2024.csv` | `observe/attendu` |

Les colonnes `n_retournees`, `ha_retournees` et `ha_retournes` gardent leur nom historique : elles comptent des conversions présumées.

## Sources et licences

| Données | Producteur | Licence | Accès |
|---|---|---|---|
| Registre parcellaire graphique 2015-2024 (parcelles graphiques) et 2025 (grandes catégories) | IGN, ASP | Licence Ouverte | service WFS `https://data.geopf.fr/wfs/ows` |
| Contours des communes et intercommunalités | API Géo, Etalab | Licence Ouverte | `https://geo.api.gouv.fr` |
| Zones humides de Normandie | DREAL Normandie | Licence Ouverte | `https://www.data.gouv.fr/fr/datasets/zones-humides-normandie/` |
| Aires d'alimentation de captages | Sandre, OFB | Licence Ouverte | `https://www.data.gouv.fr/fr/datasets/aires-dalimentation-de-captages-france-entiere-1/` |
| Habitats des sites Natura 2000 de Normandie occidentale (Calvados, Manche, Orne) | DREAL Normandie, CBN de Brest | Licence Ouverte 2.0 | `https://www.data.gouv.fr/fr/datasets/carte-de-synthese-des-habitats-dans-les-sites-natura-2000-de-normandie-occidentale-14-50-61-version-20260325/` |
| Haies (BD TOPO) | IGN | Licence Ouverte | service WFS, couche `BDTOPO_V3:haie` |

Les licences affichées par data.gouv.fr sont imprimées par le notebook 4 au moment du téléchargement.

## Confidentialité (règlement du datathon, article 9)

- Du RPG, seuls la géométrie, `code_cultu` et `code_group` sont lus et conservés (liste blanche). Les identifiants
  d'exploitants présents dans certaines couches 2024 ne sont jamais lus.
- Ne sont **jamais** publiés : les caches (`cache_rpg/`, `cache_enjeux/`), tous les fichiers `.parquet`
  (une ligne par parcelle, dont `*_PRIVE.parquet`), et les fichiers `*_INTERNE.csv` (prédictions à la commune ou à la maille
  de 1 km, échelles où la prédiction n'est pas validée).
- Les résultats publiés sont agrégés, masqués sous 5 prairies. La prédiction n'est diffusée que par interco et maille de 10 km.
  Le classement à la parcelle est réservé à un partenaire.
- Le dossier publiable est créé par la dernière cellule du notebook 4 à partir d'une liste blanche, puis contrôlé
  automatiquement (identifiants, chemins, colonnes à la parcelle).

## Limites

- Déclarations PAC : une prairie hors PAC n'est pas suivie, et un changement de déclaration ne prouve pas un labour.
- Suivi annuel, avec un an de décalage (publication du RPG).
- Pas encore de vérification de terrain ; le champ déclaratif `retournmt_` du RPG 2024 est toujours à 0 dans nos données.
- La couche Natura 2000 utilisée ne couvre que le Calvados, la Manche et l'Orne.
- Campagne 2023→24 : régime d'interdiction de retournement, annoncé fin octobre 2023 puis levé rétroactivement au
  1er janvier 2024 (arrêté du 25 novembre 2024) ; hiver 2023-2024 aussi très pluvieux. La baisse observée cette année-là
  est une observation, pas une preuve de cause à effet.

## La carte

`carte/prairie_vigie.html` est une page autonome qui intègre les données de `carte_web.zip` (notebook 4, cellule 7) :
communes (faits mesurés seulement), intercos et mailles de 10 km. Elle a été mise en forme à la main à partir de ces
fichiers ; elle n'est pas régénérée automatiquement par les notebooks.

## Licence

Code : licence MIT (`LICENSE`). Données dérivées et documentation : Licence Ouverte 2.0 (`LICENSE-DONNEES.md`).
Mention à reprendre en cas de réutilisation : « Prairie Vigie, Datathon Normandie 2026, d'après le RPG (IGN) ».
