# MSSS Urgence

Le ministère de la Santé et des Services sociaux publie plusieurs données dont celles de l'états des urgenges.

## Données

Un exemple de données extraites est disponible dans le dossier [donnees](donnees).

Un fichier CSV est rafraichi à chaque heure environ et est disponible à l'adresse suivante :
[https://www.msss.gouv.qc.ca/professionnels/statistiques/documents/urgences/Releve_horaire_urgences_7jours_nbpers.csv](https://www.msss.gouv.qc.ca/professionnels/statistiques/documents/urgences/Releve_horaire_urgences_7jours_nbpers.csv)

Le format CSV se prête mal à l'interprétation des données et ce fichier plus particulièrement comporte plusieurs irrégularités au niveau de caractère superflu. Un petit script python permet de nettoyer les données et les convertir au format json.


### Outils de développement

Vous trouverez dans le dossier [donnes/bruno](donnes/bruno) une collection qui peut être importée et utilisée avec l'outil [Bruno](https://www.usebruno.com/).



## Installation

### Home-Assistant

Les configurations Home-Assistant du projet Domo-Québec s'installent sous forme de ["package" Home-Assistant](https://www.home-assistant.io/docs/configuration/packages/). Pour faire l'activation de la fonctionnalitée créé un dossier nommé "packages" à la racine de votre dossier de configuratio Home-Assistant et ajoutez la configuration suivante à votre fichier `configuration.yaml`

```yaml
homeassistant:
  packages: !include_dir_named packages
```

Le dossier [home-assistant/packages](home-assistant/packages) contient un fichier nommé `msss.yaml` qui doit être déplacé dans le dossier "packages" de votre installation Home-Assistant.

Ensuite, copiez le fichier [home-assistant/packages](home-assistant/packages) sous le répertoire `python_scripts/`. Il sera peut-être nécessaire de créer le répertoire (dossier) `python_scripts/`.

#### Configuration
Premièrement, dans le fichier `msss.yaml` remplacer les valeurs `REGION`, par les valeurs de région disponible dans le fichier [list.md](list.md). Il est possible de rajouter des régions en ajoutant `- "<REGION ID>"` sous `json_attributes:`.

```
command_line:
  - sensor:
      name: MSSS Etat occupation des urgences
      unique_id: MSSS_urgences_data
      value_template: '{{ value_json.99.Mise_a_jour | as_datetime | as_local }}'
      device_class: timestamp
      command: 'python3 /config/python_scripts/getmsssdata.py'
      json_attributes:
      - "<REGION ID>"
      scan_interval: 3600
```

Ensuite, dans le fichier `msss.yaml` remplacer les valeurs `HOPITAL ID`, et `REGION ID` par les valeurs disponible dans le fichier [list.md](list.md).
```
 - name: msss_<HOPITAL ID>_region
      state: "{{ state_attr('sensor.msss_etat_occupation_des_urgences', '<HOPITAL ID>') [<REGION ID>].Region }}"
      availability: "{{ state_attr('sensor.msss_etat_occupation_des_urgences', '<HOPITAL ID>') is not none }}"
```

## Capteurs

Les capteurs suivant sont disponible :

  * Nom_etablissement
  * Nom_installation
  * No_permis_installation
  * Nombre_de_civieres_fonctionnelles
  * Nombre_de_civieres_occupees
  * Nombre_de_patients_sur_civiere_plus_de_24_heures
  * Nombre_de_patients_sur_civiere_plus_de_48_heures
  * Nombre_total_de_patients_presents_a_lurgence
  * Nombre_total_de_patients_en_attente_de_PEC
  * DMS_sur_civiere
  * DMS_ambulatoire
  * DMS_sur_civiere_horaire
  * DMS_ambulatoire_horaire
  * Heure_de_lextraction_image
  * Mise_a_jour

* L'acronyme PEC signifie : Prise En Charge
* L'acronyme DMS signifie : Durée Moyenne du Séjour.