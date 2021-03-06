---
title: Reference Emjpm API

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript

toc_footers:
  - <a href='https://emjpm.fabrique.social.gouv.fr/application/token-request'>Obtenez des accès de production à l'api ici</a>

includes:
  - errors

search: true
---

# Introduction

Bienvenue dans la documentation de l'API eMJPM à destination des logiciels métiers, vous trouverez ici des guides et des documentations vous permettant de commencer aussi rapidement que possible.

L'API eMJPM est organisée en REST, elle retourne des réponses encodées en JSON,
et utilise des verbes, des codes de réponses, et des méchanismes d'authentification HTTP standard.

## Environnements

**Test** [https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr](https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr)

**Production** [https://api-emjpm.fabrique.social.gouv.fr](https://api-emjpm.fabrique.social.gouv.fr)

Lors du développement de votre solution logicielle, nous vous recommandons d'utiliser l'environnement de test

[https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr](https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr)

remplacez votre client_id et votre client_token de test par ceux obtenues suite à votre demande d'autorisation

L'environnement de test est fonctionnellement identique à l'environnement de production.

## Demande de d'accès à l'API

L'API eMJPM utilise des tokens pour authentifier les requêtes via le méchanisme HTTP Bearer.

Pour utiliser l'API eMJPM en production (https://api-emjpm.fabrique.social.gouv.fr), vous devez obtenir un client_id et un client_token de production. Pour ce faire, veuillez remplir une demande d'autorisation sur notre [page dédié](https://emjpm.fabrique.social.gouv.fr/application/token-request).

# Récupérer les jetons OAuth.

## Récupérer un code d'autorisation

Pour obtenir un code d'autorisation veuillez rediriger l'utilisateur vers l'url emjpm construite de la façon suivante:

**client_id**: L’ID editor qui vous a été fourni.

**redirect_uri**: L’URL vers laquelle vous souhaitez rediriger l’utilisateur qui se connecte. Cette URL capturera la réponse provenant du login EMJPM.

**state** (optionnel): Une valeur de chaîne créée par votre app pour stabiliser la demande et le rappel. Ce paramètre doit être utilisé pour prévenir la falsification de demande intersite et vous sera renvoyé intact dans votre URI de redirection.

`https://apitest-emjpm.dev2.fabrique.social.gouv.fr/application/authorization?client_id=${votre editor_id de test}&redirect_uri=${url de redirection vers votre application}&state={chaine aléatoire}`

Si l'utilisateur n'est pas déja connecté à EMJPM, il sera invité à se connecter, puis redirigé vers le processus de connexion. Vous n’avez rien à faire de votre côté pour activer ce comportement, car il s’exécute automatiquement.


À cette étape du processus de connexion, l’utilisateur accède à un écran de permissions et peut choisir d’annuler l’opération ou d’autoriser l’app à accéder à ses données.

**Si l’utilisateur autorise l'éditeur à accéder à son compte** il sera redirigé vers:
`${url de redirection vers votre application}?code=${code_d_autorisation}`

**Si l’utilisateur refuse l'éditeur à accéder à son compte**, il sera redirigé vers:
`${url de redirection vers votre application}?error_reason=user_denied&error=access_denied&error_description=Permissions+error`

## Echanger le code d'autorisation contre un token d'accès

Lorsque le code d'autorisation est reçu, il doit être échangé contre un token d’accès à l’aide d’un point de terminaison. L’appel doit être effectué de serveur à serveur étant donné qu’il nécessite l’utilisation de votre clé secrète. (Votre clé secrète ne doit jamais se retrouver dans le code client.)

Pour obtenir un token d’accès, passez un appel HTTP GET au point de terminaison OAuth suivant :

```http
POST /api/oauth/token? HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Content-Type: application/x-www-form-urlencoded

client_id={votre_identifiant_editeur}&redirect_uri={url_de_redirection}&client_secret={votre_secret_editeur}&code={code_d_autorisation}&grant_type=authorization_code
```

Ce point de terminaison doit présenter les paramètres suivants :

**client_id**
Votre id editeur

**redirect_uri**
Cet argument est requis et doit être identique au request_uri initial que vous avez utilisé lorsque vous avez commencé le processus de connexion OAuth.

**client_secret**
Votre clé secrète unique. Cette clé secrète ne doit jamais être intégrée au code côté client ni à des fichiers binaires pouvant être décompilés. Elle doit absolument rester secrète étant donné qu’elle joue un rôle prépondérant dans la protection de votre app et de toutes les personnes qui l’utilisent.

**code**
Le paramètre reçu issu de la redirection de la boîte de dialogue Login mentionnée plus haut.

**grant_type**
doit être égal à `authorization_code`

Réponse

La réponse que vous recevez de ce point de terminaison est renvoyée au format JSON. Si le renvoi s’est bien déroulé, il s’agit de :

```json
{
    "access_token": {access-token},
    "token_type": "Bearer",
    "expires_in": {seconds-til-expiration},
    "refresh_token": {refresh-token}
}
```

L'access token vous permettra par la suite de passer des requetes sur l'api Emjpm relative à cet utilisateur et votre application.

L'utilisateur peut révoquer l'accès d'un logiciel métier à son compte eMJPM.

La durée de vie de `access_token` sera indiquée par `expires_in`, par défaut elle est de 24h. Lorsque le token aura expiré, utilisez le `refresh_token` pour obtenir un nouveau token. La durée de vie du `refresh_token` est de 2 semaines par défaut.

# Mesures

## L'objet mesure

> L'objet mesure

```json
{
  "id": 138732,
  "code_postal": "75015",
  "ville": "paris",
  "annee_naissance": 1989,
  "date_nomination": "2020-01-01",
  "date_fin_mesure": "2020-01-01",
  "numero_dossier": "354354354354",
  "numero_rg": "RG4354354354",
  "antenne_id": null,
  "latitude": 48.8402,
  "longitude": 2.29356,
  "pays": "FR",
  "lieu_vie": "etablissement",
  "type_etablissement": "autre_etablissement_s_ms",
  "civilite": "monsieur",
  "cause_sortie": "caducite",
  "date_premier_mesure": "2020-01-01",
  "date_protection_en_cours": "2020-01-01",
  "resultat_revision": "allegement",
  "etats": [
    {
      "date_changement_etat": "2020-01-01",
      "nature_mesure": "mesure_accompagnement_judiciaire",
      "champ_mesure": "protection_bien_personne",
      "lieu_vie": "etablissement",
      "code_postal": "75015",
      "ville": "paris",
      "pays": "FR",
      "type_etablissement": "autre_etablissement_s_ms"
    }
  ],
  "ressources": [
    {
      "annee": 2010,
      "niveau_ressource": 300,
      "prestations_sociales": ["PCH","RSA"]
    }
  ]
}
```

### Attributs

**id** `number`

ID de la mesure.

**annee_naissance** `number`

Année de naissance de la personne.

**date_nomination** `string`

Date de jugement ou ordonnance de nomination (yyyy-MM-dd)

**date_fin_mesure** `string`

Date de fin de la mesure de protection (yyyy-MM-dd)

**numero_dossier** `string`

Numéro de dossier de la mesure.

**numero_rg** `string`

Numéro RG de la mesure.

**antenne_id** `number`

Dans le cas d'un service mandataire, identifiant unique emjpm de l'antenne'

**latitude** `number`

**longitude** `number`

**civilite** `string`

Civilité de la personne, les valeurs possibles sont "monsieur" et "madame".

**cause_sortie** `string`

**date_premier_mesure** `string`

Date de la première mise sous protection juridique (yyyy-MM-dd)

**date_protection_en_cours** `string`

Date de la protection en cours (yyyy-MM-dd)

**resultat_revision** `string`

Résultat de la révision de la mesure de protection, les valeurs possibles sont "mainlevee", "reconduction", "aggravation", "allegement", "dessaisissement_famille", ou "dessaisissement_autre_mjpm".

**etats** `array`

Historique des états de la mesure

**etats.[*].date_changement_etat** `string`

**etats.[*].nature_mesure** `string`

**etats.[*].champ_mesure** `string`

Champ de la mesure de protection, les valeurs possibles sont "protection_bien", "protection_personne", ou "protection_bien_personne".

**etats.[*].lieu_vie** `string`

**etats.[*].code_postal** `string`

**etats.[*].pays** `string`

**etats.[*].type_etablissement** `string`

**ressources** `array`

Historique des mesures de protection

**ressources.[*].annee** `number`

**ressources.[*].niveau_ressource** `number`

**ressources.[*].prestations_sociales** `string`

## Lister toutes les mesures

> GET /api/editors/mesures

```HTTP
GET /api/editors/mesures HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr/api/editors/mesures
Authorization: Bearer {access-token}
```

Retourne la liste de toutes les mesures associées à l'utilisateur.
Les mesures retournées sont triées, avec la mesure la plus récente apparaissant en premier.

### Paramètres

**status** (optionnel)

Retourne uniquement les mesures ayant ce status. Les valeurs possibles sont "en_attente", "en_cours", ou "eteinte".

### Retours

Retourne un tableau de [mesure](/?javascript#l-39-objet-mesure).

## Récupérer une mesure

> GET /api/editors/mesures/:id

```HTTP
GET /api/editors/mesures/147853 HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Authorization: Bearer {access-token}
```

> REPONSE

```json
{
  "id": 138732,
  "annee_naissance": 1989,
  "date_nomination": "2020-01-01",
  "date_fin_mesure": "2020-01-01",
  "numero_dossier": "354354354354",
  "numero_rg": "RG4354354354",
  "antenne_id": null,
  "latitude": 48.8402,
  "longitude": 2.29356,
  "pays": "FR",
  "civilite": "monsieur",
  "cause_sortie": "caducite",
  "date_premier_mesure": "2020-01-01",
  "date_protection_en_cours": "2020-01-01",
  "resultat_revision": "allegement",
  "etats": [
    {
      "date_changement_etat": "2020-01-01",
      "nature_mesure": "mesure_accompagnement_judiciaire",
      "champ_mesure": "protection_bien_personne",
      "lieu_vie": "etablissement",
      "code_postal": "75015",
      "ville": "paris",
      "pays": "FR",
      "type_etablissement": "autre_etablissement_s_ms"
    }
  ],
  "ressources": [
    {
      "annee": 2010,
      "niveau_ressource": 300,
      "prestations_sociales": ["PCH","RSA"]
    }
  ]
}
```

Récupère la mesure associée à l'ID passé.

### Retours

Retourne l'objet [mesure](/?javascript#l-39-objet-mesure).
Si la mesure n'existe pas, une [erreur](/?javascript#errors) est retournée.

## Créer une mesure

> POST /api/editors/mesures

```HTTP
POST /api/editors/mesures HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Authorization: Bearer {access-token}
Content-Type: application/json

{
    "numero_rg": "2020202020",
    "numero_dossier": "TESTES",
    "annee_naissance": 1957,
    "civilite": "monsieur",
    "date_premier_mesure": "2019-02-14",
    "date_nomination": "2019-02-14",
    "date_protection_en_cours": "2019-04-18",
    "tribunal_siret": "17690111400080",
    "etats": [
        {
            "date_changement_etat": "2020-09-19",
            "nature_mesure": "curatelle_simple",
            "champ_mesure": "protection_bien_personne",
            "lieu_vie": "domicile",
            "code_postal": "22190",
            "ville": "PLRIN SUR MER",
            "pays": "FR",
            "type_etablissement": "etablissement_handicapes"
        }
    ],
    "ressources": [
        {
            "annee": 2019,
            "niveau_ressource": 14246,
            "prestations_sociales": []
        }
    ]
}
```

Créer une mesure avec les paramètres passés.

### Paramètres

un objet [mesure](/?javascript#l-39-objet-mesure).

### Retours

Retourne l'objet [mesure](/?javascript#l-39-objet-mesure) avec l'identifiant de la mesure créée.

## Créer des mesures

> POST /api/editors/mesures/batch

```HTTP
POST /api/editors/mesures/batch HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Authorization: Bearer {access-token}
Content-Type: application/json

{
  "mesures": [
    {
      "numero_rg": "2020202020",
      "numero_dossier": "TESTES",
      "annee_naissance": 1957,
      "civilite": "monsieur",
      "date_premier_mesure": "2019-02-14",
      "date_nomination": "2019-02-14",
      "date_protection_en_cours": "2019-04-18",
      "tribunal_siret": "17690111400080",
      "etats": [
        {
          "date_changement_etat": "2020-09-19",
          "nature_mesure": "curatelle_simple",
          "champ_mesure": "protection_bien_personne",
          "lieu_vie": "domicile",
          "code_postal": "22190",
          "ville": "PLRIN SUR MER",
          "pays": "FR",
          "type_etablissement": "etablissement_handicapes"
        }
      ],
      "ressources": [
        {
          "annee": 2019,
          "niveau_ressource": 14246,
          "prestations_sociales": []
        }
      ]
    },
    {
      "numero_rg": "18/A/3245",
      "numero_dossier": "20190512",
      "annee_naissance": 1982,
      "civilite": "monsieur",
      "date_premier_mesure": "2014-01-01",
      "date_nomination": "2018-02-01",
      "date_protection_en_cours": "2020-05-12",
      "tribunal_siret": "17690111400080",
      "etats": [
        {
          "date_changement_etat": "2020-05-12",
          "nature_mesure": "curatelle_renforcee",
          "champ_mesure": "protection_bien_personne",
          "lieu_vie": "etablissement",
          "code_postal": "22190",
          "ville": "PLRIN SUR MER",
          "pays": "FR",
          "type_etablissement": "autre_etablissement_s_ms"
        },
        {
          "date_changement_etat": "2019-09-19",
          "nature_mesure": "curatelle_simple",
          "champ_mesure": "protection_bien_personne",
          "lieu_vie": "domicile",
          "code_postal": "75005",
          "ville": "PARIS",
          "pays": "FR"
        },
        {
          "date_changement_etat": "2018-02-01",
          "nature_mesure": "curatelle_simple",
          "champ_mesure": "protection_personne",
          "lieu_vie": "domicile",
          "code_postal": "75005",
          "ville": "PARIS",
          "pays": "FR"
        }
      ],
      "ressources": [
        {
          "annee": 2020,
          "niveau_ressource": 25000,
          "prestations_sociales": []
        },
        {
          "annee": 2019,
          "niveau_ressource": 28000,
          "prestations_sociales": []
        },
        {
          "annee": 2018,
          "niveau_ressource": 30000,
          "prestations_sociales": []
        }
      ]
    }
  ]
}
```

Créer un tableau de mesures avec les paramètres passés.

### Paramètres

un tableau d'objet [mesure](/?javascript#l-39-objet-mesure).

### Retours

Retourne le tableau d'objet [mesure](/?javascript#l-39-objet-mesure) avec les identifiants des mesures crées.

## Modifier une mesure

> PUT /api/editors/mesures/:id

```HTTP
PUT /api/editors/mesures/147853 HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Authorization: Bearer {access-token}
Content-Type: application/json

{
    "id": 147853
    "numero_rg": "2020202020",
    "numero_dossier": "TESTES",
    "annee_naissance": 1957,
    "civilite": "madame",
    "date_premier_mesure": "2019-02-14",
    "date_nomination": "2019-02-14",
    "date_protection_en_cours": "2019-04-18",
    "tribunal_siret": "17690111400080",
    "etats": [
        {
            "date_changement_etat": "2020-09-19",
            "nature_mesure": "curatelle_simple",
            "champ_mesure": "protection_bien_personne",
            "lieu_vie": "domicile",
            "code_postal": "22190",
            "ville": "PLRIN SUR MER",
            "pays": "FR",
            "type_etablissement": "etablissement_handicapes"
        }
    ],
    "ressources": [
        {
            "annee": 2019,
            "niveau_ressource": 14246,
            "prestations_sociales": []
        },
        {
            "annee": 2018,
            "niveau_ressource": 15500,
            "prestations_sociales": ["PCH","RSA"]
        }
    ]
}
```

Modifie la mesure avec les paramètres passés.

### Paramètres

un objet [mesure](/?javascript#l-39-objet-mesure).

### Retours

Retourne l'objet [mesure](/?javascript#l-39-objet-mesure).
Si la mesure n'existe pas, une [erreur](/?javascript#errors) est retournée.

## Supprimer une mesure

> DELETE /api/editors/mesures/:id

```HTTP
DELETE /api/editors/mesures/147853 HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Authorization: Bearer {access-token}
```

> REPONSE

```json
{
  "affected_rows": 1
}
```

Supprime la mesure associé à l'ID passé.

### Retours

Retourne le nombre de mesures [mesure](/?javascript#l-39-objet-mesure) supprimées.
Si la mesure n'existe pas, une [erreur](/?javascript#errors) est retournée.

## Supprimer toutes les mesure

> DELETE /api/editors/mesures

```HTTP
DELETE /api/editors/mesures HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Authorization: Bearer {access-token}
```

> REPONSE

```json
{
  "affected_rows": 10
}
```

Supprime toutes les mesures de l'utilisateur

### Retours

Retourne le nombre de mesures [mesure](/?javascript#l-39-objet-mesure) supprimées.

# Antennes (service)

## Lister toutes les antennes

> GET /api/editors/service-antennes

```HTTP
HET /api/editors/service-antennes HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Authorization: Bearer {access-token}
```

> RESPONSE

```json
[
  {
    "id": 175,
    "nom": "OULLINS",
    "adresse": "12 RUE JACQUARD",
    "code_postal": "69600",
    "ville": "OULLINS"
  },
  {
    "id": 176,
    "nom": "RILLIEUX",
    "adresse": "1 RUE GABRIEL LADEVEZE",
    "code_postal": "69140",
    "ville": "RILLIEUX-LA-PAPE"
  },
  {
    "id": 174,
    "nom": "GRIGNY",
    "adresse": "10 RUE ROBESPIERRE",
    "code_postal": "69520",
    "ville": "GRIGNY"
  }
]
```

Retourne la liste de toutes les antennes associées à votre service.

# Tribunaux

## Lister tous les tribunaux

> GET /api/editors/tribunaux

```HTTP
HET /api/editors/tribunaux HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Authorization: Bearer {access-token}
```

> RESPONSE

```json
[
    {
        "nom": "Tribunal judiciaire de Valence",
        "code_postal": "26000",
        "ville": "VALENCE",
        "siret": "17380111900020",
        "adresse": "2 place Simone VEIL",
        "latitude": 44.923,
        "longitude": 4.91444
    },
    {
        "nom": "Tribunal judiciaire de Cayenne",
        "code_postal": "97300",
        "ville": "CAYENNE",
        "siret": "13001580300021",
        "adresse": "15 avenue du Général de Gaulle",
        "latitude": 5.02174,
        "longitude": -52.5013
    },
    {
        "nom": "Tribunal judiciaire de La Roche-sur-Yon",
        "code_postal": "85000",
        "ville": "LA ROCHE SUR YON",
        "siret": "17860111800023",
        "adresse": "55 Boulevard Aristide Briand",
        "latitude": 46.6675,
        "longitude": -1.4078
    },
    {
        "nom": "Tribunal judiciaire de Dieppe",
        "code_postal": "76200",
        "ville": "DIEPPE",
        "siret": "17760111900023",
        "adresse": "Square Carnot",
        "latitude": 49.9222,
        "longitude": 1.08681
    },
    {
        "nom": "Tribunal judiciaire d'Aix-en-Provence",
        "code_postal": "13080",
        "ville": "AIX EN PROVENCE",
        "siret": "17130111200024",
        "adresse": "40 Boulevard Carnot",
        "latitude": 43.5361,
        "longitude": 5.39857
    },
    {
        "nom": "Tribunal judiciaire de Guéret",
        "code_postal": "23000",
        "ville": "GUERET",
        "siret": "17870111600026",
        "adresse": "23 Place Bonnyaud",
        "latitude": 46.1632,
        "longitude": 1.87079
    },
    {
        "nom": "Tribunal judiciaire de Saint-Denis-de-La-Réunion",
        "code_postal": "97400",
        "ville": "ST DENIS",
        "siret": "17974111100027",
        "adresse": "5 avenue André Malraux",
        "latitude": -20.933,
        "longitude": 55.4469
    },
    {
        "nom": "Tribunal judiciaire de Cherbourg-en-Cotentin",
        "code_postal": "50460",
        "ville": "CHERBOURG EN COTENTIN",
        "siret": "17140111000027",
        "adresse": "15 rue des Tribunaux",
        "latitude": 49.6334,
        "longitude": -1.6339
    },
    {
        "nom": "Tribunal judiciaire d'Albertville",
        "code_postal": "73200",
        "ville": "ALBERTVILLE",
        "siret": "17730111600030",
        "adresse": "avenue des Chasseurs Alpins",
        "latitude": 45.6684,
        "longitude": 6.4046
    },
    {
        "nom": "Tribunal judiciaire des Sables-d'Olonne",
        "code_postal": "85100",
        "ville": "LES SABLES D OLONNE",
        "siret": "17860111800031",
        "adresse": "Place du Palais de Justice",
        "latitude": 46.5008,
        "longitude": -1.79255
    },
    {
        "nom": "Tribunal judiciaire de Saint-Pierre",
        "code_postal": "97410",
        "ville": "ST PIERRE",
        "siret": "17974111100035",
        "adresse": "28 rue Augustin Archambaud",
        "latitude": -21.3123,
        "longitude": 55.4936
    },
    {
        "nom": "Tribunal judiciaire de Basse-Terre",
        "code_postal": "97100",
        "ville": "BASSE TERRE",
        "siret": "17971111400035",
        "adresse": "4 Boulevard Félix Eboué",
        "latitude": 16.0005,
        "longitude": -61.7281
    },
    {
        "nom": "Tribunal judiciaire de Tarbes",
        "code_postal": "65000",
        "ville": "TARBES",
        "siret": "17640111500041",
        "adresse": "6 bis rue du Maréchal Foch",
        "latitude": 43.2348,
        "longitude": 0.0660094
    },
    {
        "nom": "Tribunal judiciaire de Reims",
        "code_postal": "51100",
        "ville": "REIMS",
        "siret": "17510111200043",
        "adresse": "Place Myron Herrick",
        "latitude": 49.2515,
        "longitude": 4.04023
    },
    {
        "nom": "Tribunal judiciaire d'Auch",
        "code_postal": "32000",
        "ville": "AUCH",
        "siret": "17470111000044",
        "adresse": "Allée d'Etigny",
        "latitude": 43.6534,
        "longitude": 0.57519
    },
    {
        "nom": "Tribunal de Première Instance de Mata-Utu",
        "code_postal": "98600",
        "ville": "UVEA",
        "siret": "13000313000049",
        "adresse": null,
        "latitude": 48.8534,
        "longitude": 2.3488
    },
    {
        "nom": "Tribunal judiciaire d'Agen",
        "code_postal": "47000",
        "ville": "AGEN",
        "siret": "17470111000051",
        "adresse": "Avenue de Lattre de Tassigny",
        "latitude": 44.2028,
        "longitude": 0.625584
    },
    {
        "nom": "Tribunal judiciaire de Vienne",
        "code_postal": "38200",
        "ville": "VIENNE",
        "siret": "17380111900053",
        "adresse": "16 Place Charles de Gaulle",
        "latitude": 45.5206,
        "longitude": 4.88135
    },
    {
        "nom": "Tribunal judiciaire de Châteauroux",
        "code_postal": "36000",
        "ville": "CHATEAUROUX",
        "siret": "17180111100054",
        "adresse": "Place Lucien Germereau",
        "latitude": 46.803,
        "longitude": 1.694
    },
    {
        "nom": "Tribunal judiciaire de Béthune",
        "code_postal": "62400",
        "ville": "BETHUNE",
        "siret": "17590111500055",
        "adresse": "Place Lamartine",
        "latitude": 50.5289,
        "longitude": 2.64242
    },
    {
        "nom": "Tribunal judiciaire de Poitiers",
        "code_postal": "86000",
        "ville": "POITIERS",
        "siret": "17860111800056",
        "adresse": "4 Bld Maréchal de Lattre de Tassigny",
        "latitude": 46.5839,
        "longitude": 0.359948
    },
    {
        "nom": "Tribunal judiciaire d'Angers",
        "code_postal": "49000",
        "ville": "ANGERS",
        "siret": "17490111600056",
        "adresse": "Rue Waldeck Rousseau",
        "latitude": 47.4768,
        "longitude": -0.556126
    }
]
```

Retourne la liste de toutes les antennes associées à votre service.


# Information utilisateur

## Récupérer les informations de l'utilisateur courant

Retourne les informations de l'utilisateur courant

> GET /api/mandoline/user

```HTTP
HET /api/mandoline/user HTTP/1.1
Host: https://api-apitest-emjpm.dev2.fabrique.social.gouv.fr
Authorization: Bearer {access-token}
```

> RESPONSE

```json

{
  id: 979,
  type: "direction",
  email: "direction-979@justice.fr",
  nom: "direction",
  prenom: "Paula",
  direction: {
    departement: { code: "75", nom: "Paris" },
    type: "departemental",
  }
}
```

```json
{
  id: 2042,
  type: "service",
  email: "service-2042@justice.fr",
  nom: "service",
  prenom: "Paula",
  service: {
    departement: { code: "75", nom: "Paris" },
    dispo_max: 325,
    email: "service-50@justice.fr",
    etablissement: "service-50",
    lb_adresse: "Rue du service tutellaire",
    lb_code_postal: "75010",
    lb_ville: "Paris",
    mesures_en_attente: 330,
    mesures_en_cours: 0,
    nom: null,
    org_adresse: "Rue de l'organisme gestionnaire",
    org_code_postal: null,
    org_gestionnaire: null,
    org_nom: "Organisme gestionnaire",
    org_ville: null,
    prenom: null,
    siret: null,
    telephone: "0140506070",
  }
```
