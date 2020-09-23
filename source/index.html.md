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

client_id={votre_identifiant_editeur}&redirect_uri={url_de_redirection}&client_secret={votre_secret_editeur}code={code_d_autorisation}&grant_type=authorization_code
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
      "type_etablissement": "autre_etablissement_s_ms",
      "etablissement_siret": ""
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

**etats.[*].etablissement_siret** `string`

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
      "type_etablissement": "autre_etablissement_s_ms",
      "etablissement_siret": ""
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
