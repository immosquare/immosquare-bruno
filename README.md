---
locale: fr
tags:
  - app:immosquare-bruno
  - audience:technique
---

# immosquare-bruno

Mono-repo regroupant les **collections [Bruno](https://www.usebruno.com/)** d'immosquare. Bruno est un client API open-source (alternative à Postman) qui stocke chaque requête dans un fichier `.bru` versionné — ce qui permet de tout suivre dans Git.

Ce dépôt centralise les 7 collections qui étaient auparavant éparpillées dans des repos séparés sous `~/Sites/bruno/`. L'objectif est de simplifier la maintenance : un seul `git pull`, un seul historique, des conventions partagées.

## Les 7 collections Bruno du dépôt

Chaque dossier racine est une **collection Bruno autonome**, avec son propre `bruno.json` et ses environnements. Les collections qui en ont besoin documentent leurs spécificités dans un `CLAUDE.md` local (voir `centris/CLAUDE.md` et `monitoring/CLAUDE.md`). Chaque ligne donne le nombre de requêtes de la collection et l'API ou le périmètre qu'elle couvre.

| Collection                          | Requêtes | Description                                                            |
| ----------------------------------- | ---:     | ---------------------------------------------------------------------- |
| [`appgenerator/`](./appgenerator)   | 2        | Génération d'apps internes (listglobally, pachamama)                   |
| [`centris/`](./centris)             | 98       | API **Centris Data Distribution** (RESO) — listings immobiliers Québec |
| [`elasticsearch/`](./elasticsearch) | 5        | Requêtes Elasticsearch (dev / preprod / prod)                          |
| [`mlsconnect/`](./mlsconnect)       | 87       | API MlsConnect — v1, v2, v3, OAuth, MCP, webhooks                      |
| [`monitoring/`](./monitoring)       | 12       | Health checks, emails, Slack, gateways, DNS, Ubiflow                   |
| [`shareimmo/`](./shareimmo)         | 29       | API Shareimmo v2 + visibilités                                         |
| [`storeimmo/`](./storeimmo)         | 6        | API Storeimmo + Immofacile                                             |

## Ouvrir une collection du dépôt dans Bruno

1. Installer Bruno : <https://www.usebruno.com/downloads>
2. Cloner le dépôt :
   ```sh
   git clone git@github.com:immosquare/immosquare-bruno.git
   ```
3. Dans Bruno : **Open Collection** → sélectionner le sous-dossier de la collection voulue (ex. `mlsconnect/`).
4. Choisir un environnement dans le sélecteur en haut à droite (ex. `develop`, `staging`, `production`).
5. Renseigner les variables `secret` côté UI Bruno (tokens, mots de passe) — elles ne sont **jamais** committées.

> Tu peux ouvrir plusieurs collections en parallèle dans Bruno (une par onglet).

## Format des fichiers `.bru` et conventions du dépôt

Un fichier `.bru` est un texte structuré par blocs. Exemple minimal :

```
meta {
  name: Get Property
  type: http
  seq: 1
}

get {
  url: {{baseurl}}/odata/Property
  body: none
  auth: bearer
}

auth:bearer {
  token: {{apiKey}}
}
```

Les blocs courants : `meta`, `get`/`post`/`put`/`delete`, `headers`, `auth:bearer`, `body:json`, `query`, `vars:pre-request`, `script:pre-request`, `script:post-response`, `tests`. Les `{{variable}}` référencent les variables d'environnement définies dans `<collection>/environments/<env>.bru`.

Les conventions que suivent toutes les collections du dépôt :

- **Une collection = un dossier racine.** Ne pas mélanger les requêtes de plusieurs APIs dans la même collection.
- **Variables secrètes** (`vars:secret`) : déclarées dans le fichier d'environnement mais leur valeur reste locale (Bruno UI). Ne jamais committer un token en clair.
- **`folder.bru`** : définit l'auth héritée et la doc d'un sous-dossier — privilégier l'héritage plutôt que de répéter `auth:bearer` dans chaque requête.
- **Séquence (`seq`)** : numéroter les requêtes dans `meta` pour garder un ordre logique dans la sidebar Bruno.
- **Fichiers ignorés** : `.DS_Store`, `node_modules/`, `.vscode/`, `.claude/`, `.ralph-loop.local.md` (voir `.gitignore`).
