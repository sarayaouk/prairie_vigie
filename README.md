# Prairie Vigie

**Où disparaissent les prairies de Normandie, et lesquelles protéger en premier ?**

Prairie Vigie est une carte en ligne qui montre, commune par commune, où les prairies permanentes ont été transformées
en cultures depuis 2019, et où agir en priorité pour la campagne 2027. Elle s'adresse aux intercommunalités, aux syndicats
d'eau, aux parcs naturels, aux services de l'État et aux associations.

Projet réalisé pour le Datathon Normandie 2026 (« Biodiversité et activité humaine en Normandie ») par
**Sara CHEKLAT et Mariem GAALICHE**, Master 2 « Apprentissage automatique pour l'IA », Université de Rouen Normandie.

👉 **Voir la carte : https://sarayaouk.github.io/prairie_vigie/carte/prairie_vigie.html**

## Le problème

Une prairie permanente, c'est de l'herbe en place depuis au moins cinq ans. Elle abrite fleurs, insectes et oiseaux,
filtre l'eau et stocke du carbone. Quand elle est labourée pour semer une culture, ces bienfaits disparaissent pour longtemps.
En Normandie, les prairies reculent. On sait combien au niveau régional, mais personne ne peut voir facilement **où** cela se
passe, ni **quelles prairies** protéger d'abord.

## Ce que fait Prairie Vigie

La carte répond à trois questions :

1. **Où les prairies ont-elles été converties ?** Chaque année, nous comparons les parcelles déclarées à la PAC avec celles de
   l'année suivante. Quand plus de la moitié d'une prairie est déclarée en culture l'année d'après, nous comptons une
   **conversion présumée**. « Présumée », car c'est une déclaration, pas un constat sur le terrain.
2. **Où risquent-elles de l'être ?** Un modèle simple (régression logistique) repère les prairies les plus menacées, deux ans
   à l'avance, car les déclarations d'une année ne sont publiées que l'année suivante.
3. **Lesquelles protéger en premier ?** Parmi les prairies les plus menacées, nous gardons celles qui protègent aussi l'eau
   ou la biodiversité : zone humide, aire de captage d'eau potable ou habitat Natura 2000.

## Les chiffres clés

- **Environ 49 700 hectares** de prairies convertis en cultures entre 2019 et 2025, pour seulement **4 630 hectares** recréés :
  près de **11 hectares perdus pour 1 regagné**. Sept hectares convertis sur dix deviennent du maïs.
- **Nos totaux sont fiables** : ils ne s'écartent que de +0,5 % (2018) et +0,4 % (2025) des surfaces officielles.
- **Le modèle aide à cibler les prairies** : dans les 10 % de prairies qu'il signale, on retrouve 25,8 % des conversions
  survenues deux ans plus tard, contre 17,3 % en regardant seulement le passé du secteur.
- **Mais pour comparer les communes entre elles, le passé suffit** : là, le modèle ne fait pas mieux que l'historique.
  La carte publique montre donc l'historique observé, plus simple et tout aussi fiable.
- **12 354 hectares prioritaires** (6 488 prairies), soit environ 2 % des prairies normandes, cumulent un risque élevé et au
  moins un enjeu pour l'eau ou la biodiversité. Dont 6 466 hectares (2 885 prairies) avec au moins deux enjeux.

## Voir les résultats sans rien installer

- **La carte** : le lien ci-dessus, ou le fichier `carte/prairie_vigie.html` à ouvrir dans un navigateur.
- **Les tableaux** : les fichiers `.csv` du dossier `donnees/` s'ouvrent dans un tableur (Excel, LibreOffice, Google Sheets).
  Chaque fichier et chaque colonne sont expliqués dans `donnees/DICTIONNAIRE_DES_DONNEES.md`.
- **Pour un logiciel de cartographie (un SIG, « système d’information géographique », comme QGIS)** : `donnees/communes.geojson`, `intercos.geojson` et `mailles10km.geojson`.

Il n'est utile de relancer les calculs que pour les vérifier, ou pour les mettre à jour quand une nouvelle année de
déclarations PAC est publiée.

## Refaire les calculs

Les calculs tiennent dans **5 notebooks Python**, prévus pour Google Colab, à lancer **dans l'ordre**.

1. Ouvrir le notebook dans Colab, puis « Exécution → Tout exécuter ».
2. Accepter l'accès à Google Drive : tous les notebooks travaillent dans le même dossier de votre Drive
   (`datathon_prairies`, modifiable dans la première cellule).
3. Attendre la fin avant de lancer le suivant. Si la connexion coupe, il suffit de relancer : ce qui est déjà téléchargé
   ou calculé n'est pas refait.

Sans Colab : `pip install -r requirements.txt`, puis Jupyter ; les notebooks travaillent alors dans le dossier courant.

| Ordre | Notebook | Ce qu'il fait | Durée mesurée |
|---|---|---|---|
| 1 | `1_mesure_2015_2023_v3.ipynb` | Télécharge les déclarations PAC 2015-2023, compte les conversions 2019→2023, teste un premier modèle | environ 1 h 20 la première fois (presque tout en téléchargement) |
| 2 | `2_mesure_2024_2025_v4.ipynb` | Ajoute 2024 et 2025, et vérifie les prédictions sur des années que le modèle n'a jamais vues | environ 35 min la première fois |
| 3 | `3_consolidation_v5.ipynb` | Vérifie la qualité des conversions, choisit le modèle, estime les hectares menacés en 2027, mesure l'énergie consommée | environ 6 min |
| 4 | `4_prioriser_et_carte.ipynb` | Croise avec les zones humides, captages, Natura 2000 et haies ; calcule les priorités ; prépare la carte et le dossier publiable | environ 20 min la première fois (surtout les haies) |
| 5 | `5_indice_vulnerabilite.ipynb` | Teste deux indices de vulnérabilité par commune (voir plus bas) | environ 2 min |

Les tirages au hasard utilisent une graine fixe : les résultats sont identiques d'une exécution à l'autre. Les versions
exactes des bibliothèques sont dans `requirements.txt`. Seule réserve : si un producteur met à jour ses données, de petits
écarts sont possibles (par exemple +0,8 % sur les priorités après une mise à jour des aires de captage).

## Où trouver chaque chiffre

Tous les fichiers sont dans `donnees/`.

| Chiffre | Fichier | Comment le lire |
|---|---|---|
| Surfaces de prairies en 2018 et 2025 | `surfaces_par_dep_annee_2015_2025.csv` | additionner la colonne `pp` pour l'année voulue |
| Conversions et créations par année | `bilan_transitions_2019_2025.csv` | additionner `ha_retournees` (conversions) et `ha_gains` (créations) |
| Prairies sorties des déclarations | même fichier | `n_sorties_rpg` : c'est un **nombre de prairies**, pas des hectares |
| Part des conversions encore cultivées deux ans après | `qualite_etiquettes.csv` | colonne `encore_cultivees_N+2` |
| Performance du modèle | `evaluation_complete.csv` | ligne « 2 ans, 2023 -> 2024->25 », Normandie, colonne `lift_10` |
| Modèle contre historique | `valeur_ajoutee_modele_vs_historique.csv` (communes, intercos), `valeur_ajoutee_parcelle.csv` (prairies) | comparer les colonnes `modele` et `historique` |
| Prairies prioritaires | `priorites_synthese_normandie.csv` | `n_prairies` et `ha` pour les niveaux 1 et 2 |
| Campagne 2023-2024 | `effet_regime_2023_2024.csv` | colonne `observe/attendu` |
| Indices de vulnérabilité | `indice_vulnerabilite_test.csv`, `indice_pression_fourragere_test.csv` | colonne `spearman` ; la décision est sur la ligne de l'indice |

Les colonnes qui contiennent « retourn » (`ha_retournees`, `ha_retournes`…) gardent un ancien nom : elles comptent bien des
conversions présumées.

## Ce que nous avons testé et écarté

Nous avons construit un **indice de vulnérabilité par commune**. Nous l'avons testé avec une
règle écrite **avant** de voir le résultat : un indice n'est retenu que s'il repère mieux les communes touchées en 2025 que
le simple historique, avec une marge d'au moins 0,05 (sur une échelle où 1 serait un classement parfait).

- **Première version** (peu de prairies, recul récent, conversions passées, proximité des grandes
  cultures) : 0,13 contre 0,29 pour l'historique. **Non retenu.** : être près des grandes cultures ne rend pas les
  prairies plus menacées.
- **Un indice tiré des études régionales** (Agreste Normandie 2021, DRAAF Bretagne 2025), fondé sur le **maïs**, nourriture des
  élevages laitiers : 0,32, à peine mieux que l'historique, sous la marge exigée. **Non retenu.**
- **Ce que nous en retenons** : la part de maïs dans une commune suffit à prédire aussi bien que l'historique (0,31). La
  pression sur les prairies vient surtout de l'élevage laitier du bocage, comme le décrivent ces études. Ce test est
  exploratoire : il faudra le confirmer sur la campagne 2025-2026.

## D'où viennent les données

Toutes les données sont publiques et ouvertes.

| Données | Producteur | Licence | Où les trouver |
|---|---|---|---|
| Déclarations PAC : Registre parcellaire graphique 2015-2025 | IGN, ASP | Licence Ouverte | service WFS `https://data.geopf.fr/wfs/ows` |
| Contours des communes et intercommunalités | API Géo, Etalab | Licence Ouverte | `https://geo.api.gouv.fr` |
| Zones humides de Normandie | DREAL Normandie | Licence Ouverte | `https://www.data.gouv.fr/fr/datasets/zones-humides-normandie/` |
| Aires d'alimentation de captages d'eau potable | Sandre, OFB | Licence Ouverte | `https://www.data.gouv.fr/fr/datasets/aires-dalimentation-de-captages-france-entiere-1/` |
| Habitats Natura 2000 de Normandie occidentale (Calvados, Manche, Orne) | DREAL Normandie, CBN de Brest | Licence Ouverte 2.0 | `https://www.data.gouv.fr/fr/datasets/carte-de-synthese-des-habitats-dans-les-sites-natura-2000-de-normandie-occidentale-14-50-61-version-20260325/` |
| Haies (BD TOPO) | IGN | Licence Ouverte | service WFS, couche `BDTOPO_V3:haie` |

## Données personnelles : ce que nous protégeons

- Des déclarations PAC, nous ne lisons que la forme de la parcelle et sa culture. **Aucun identifiant d'agriculteur** n'est lu
  ni conservé.
- La carte publique s'arrête à la commune. Rien n'est affiché pour moins de 5 prairies.
- Les prédictions ne sont publiées qu'à l'échelle des intercommunalités et des carrés de 10 km. Le classement prairie par
  prairie n'est jamais publié.
- Les fichiers de travail détaillés (dossiers `cache_…`, fichiers `.parquet`, `_PRIVE`, `_INTERNE`) restent sur le Drive de
  l'équipe et ne sont jamais mis en ligne.

## Ce qu'il faut savoir avant d'utiliser les chiffres

- Ce sont des **déclarations**, pas des constats de terrain. Une prairie hors PAC n'est pas suivie.
- Les données ont **un an de décalage** : les déclarations d'une année sont publiées l'année suivante.
- La couche Natura 2000 utilisée ne couvre que le Calvados, la Manche et l'Orne.
- **2023** : avec la nouvelle PAC, beaucoup plus de prairies que d'habitude sont sorties des déclarations (13 015, contre environ 3 300
  à 4 100 les autres années). Nous ne les comptons pas comme des conversions.
- **2023-2024** : un régime d'interdiction de retourner les prairies a été annoncé fin octobre 2023, puis levé
  rétroactivement au 1er janvier 2024 (arrêté du 25 novembre 2024). Les conversions ont nettement baissé cette année-là, mais
  l'hiver a aussi été très pluvieux : c'est une observation, pas une preuve.

## Énergie consommée

Nous avons mesuré la consommation de l'étape de modélisation (notebook 3) avec l'outil CodeCarbon : **6 minutes,
0,001 kWh, 0,2 g de CO2**, une fois les données téléchargées (`donnees/sobriete_v5.csv`). Le téléchargement initial
(environ 1 h 45 au total) n'est fait qu'une fois. Nous avons aussi choisi le modèle le plus simple quand il faisait aussi
bien qu'un modèle plus lourd.

## La carte

`carte/prairie_vigie.html` est une page autonome, sans serveur. Elle contient les données préparées par le notebook 4
(`carte_web.zip`), mises en forme à la main : elle n'est pas régénérée automatiquement par les notebooks.

## Licence

- Code : licence MIT (`LICENSE`).
- Données produites et documentation : Licence Ouverte 2.0 (`LICENSE-DONNEES.md`).
