# Dictionnaire des données – Prairie Vigie
Sources : RPG parcelles graphiques 2015-2024 et grandes catégories 2025 (IGN, Licence Ouverte) ; communes et intercos (API Géo, Etalab) ;
zones humides (DREAL Normandie), aires d'alimentation de captages (Sandre), habitats Natura 2000 de Normandie occidentale, haies (BD TOPO, IGN).

Définitions
- Prairie permanente : code_group 18 du RPG.
- Conversion présumée : prairie permanente dont plus de 50 % de la surface est déclarée en terre arable l'année suivante
  (déclarations PAC, sans constat de terrain). Les colonnes n_retournees, ha_retournees et ha_retournes gardent leur nom
  historique : elles comptent des conversions présumées.
- Création : prairie permanente dont plus de 50 % de la surface était déclarée en terre arable l'année précédente.
- Sortie du RPG : prairie permanente absente des déclarations l'année suivante (n_sorties_rpg = nombre de prairies, pas des hectares).
- Prairie ancienne : jamais cultivée depuis 2015.
Aucune donnée personnelle ; résultats masqués sous 5 prairies. Prédictions diffusées seulement par interco et maille de 10 km.

Fichiers diffusables
- surfaces_par_dep_annee_2015_2025.csv : surfaces par classe (pp, herbe, arable, gel, autre) en ha, par département et année
- bilan_transitions_2019_2025.csv : par département et transition, n_pp, ha_pp, n_retournees, ha_retournees, n_sorties_rpg, n_gains, ha_gains
- observatoire_communes_2019_2025.csv : ha_retournes (conversions présumées) et ha_crees par commune et transition
- qualite_etiquettes.csv : persistance à 2 ans des conversions, sorties du RPG et conversions différées
- cultures_de_remplacement.csv : cultures déclarées l'année qui suit une conversion
- concordance_retournement_declare_2024.csv : comparaison au champ déclaratif retournmt_ du RPG 2024 (exploratoire ; champ toujours à 0 dans nos données)
- resultats_modeles_normandie.csv, verification_prediction_2025.csv, evaluation_complete.csv : performances (lift, rappel, AUC-PR)
- effet_regime_2023_2024.csv : hectares observés vs attendus, campagne 2022→23 (témoin) et 2023→24
- epci_hectares_attendus_2027.csv : hectares attendus convertis lors de la campagne 2027 par interco (intervalle à 90 %)
- importance_variables.csv, figures/*.png : explicabilité et calibration
- sobriete_v5.csv : durée et énergie du notebook 3 ; versions_bibliotheques.txt : versions utilisées
- Notebook 4 : enjeux_communes_2025.csv (faits mesurés, sans prédiction), priorites_epci_2027.csv, priorites_maille10km_2027.csv,
  priorites_synthese_normandie.csv, valeur_ajoutee_modele_vs_historique.csv (test 5 bis), valeur_ajoutee_parcelle.csv (test 5 ter),
  communes_bilan_prairies_2019_2025.csv, communes/intercos/mailles10km.geojson (carte)

Fichiers internes, jamais diffusés : caches, tous les .parquet (dont *_PRIVE), fichiers *_INTERNE.csv.

- Notebook 5 : indice_vulnerabilite_test.csv (test de l'indice de vulnérabilité communal : corrélation de Spearman avec les conversions 2024→25, gain face à l'historique, intervalle à 95 %, décision)
- Notebook 5 : indice_pression_fourragere_test.csv (test exploratoire d'un indice fondé sur le maïs déclaré, d'après les études Agreste Normandie 2021 et DRAAF Bretagne 2025 : corrélations, gain face à l'historique, décision)
