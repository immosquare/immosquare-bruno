# immosquare-bruno

Mono-repo regroupant les **collections [Bruno](https://www.usebruno.com/)** d'Immosquare. Bruno est un client API open-source (alternative à Postman) qui stocke chaque requête dans un fichier `.bru` versionné — ce qui permet de tout suivre dans Git.

Ce dépôt centralise les 7 collections qui étaient auparavant éparpillées dans des repos séparés sous `~/Sites/bruno/`. L'objectif est de simplifier la maintenance : un seul `git pull`, un seul historique, des conventions partagées.

## Collections

| Collection | Requêtes | Description |
|---|---:|---|
| [`appgenerator/`](./appgenerator) | 2 | Génération d'apps internes (listglobally, pachamama) |
| [`centris/`](./centris) | 100 | API **Centris Data Distribution** (RESO) — listings immobiliers Québec |
| [`elasticsearch/`](./elasticsearch) | 9 | Requêtes Elasticsearch (dev / preprod / prod) |
| [`mlsconnect/`](./mlsconnect) | 110 | API MlsConnect — v1, v2, v3, OAuth, MCP, webhooks |
| [`monitoring/`](./monitoring) | 18 | Health checks, emails, Slack, gateways, DNS, Ubiflow |
| [`shareimmo/`](./shareimmo) | 38 | API Shareimmo v2 + visibilités |
| [`storeimmo/`](./storeimmo) | 7 | API Storeimmo + Immofacile |

Chaque dossier est une **collection Bruno autonome** avec son propre `bruno.json` et ses environnements. Les collections qui en ont besoin documentent leurs spécificités dans un `CLAUDE.md` local (voir `centris/CLAUDE.md` et `monitoring/CLAUDE.md`).

## Utilisation

1. Installer Bruno : <https://www.usebruno.com/downloads>
2. Cloner le dépôt :
   ```sh
   git clone git@github.com:immosquare/immosquare-bruno.git
   ```
3. Dans Bruno : **Open Collection** → sélectionner le sous-dossier de la collection voulue (ex. `mlsconnect/`).
4. Choisir un environnement dans le sélecteur en haut à droite (ex. `develop`, `staging`, `production`).
5. Renseigner les variables `secret` côté UI Bruno (tokens, mots de passe) — elles ne sont **jamais** committées.

> Tu peux ouvrir plusieurs collections en parallèle dans Bruno (une par onglet).

## Structure d'un fichier `.bru`

Format texte structuré par blocs. Exemple minimal :

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

Les blocs courants : `meta`, `get`/`post`/`put`/`delete`, `headers`, `auth:bearer`, `body:json`, `query`, `vars:pre-request`, `script:pre-request`, `script:post-response`, `tests`.

Les `{{variable}}` référencent les variables d'environnement définies dans `<collection>/environments/<env>.bru`.

## Conventions

- **Une collection = un dossier racine.** Ne pas mélanger les requêtes de plusieurs APIs dans la même collection.
- **Variables secrètes** (`vars:secret`) : déclarées dans le fichier d'environnement mais leur valeur reste locale (Bruno UI). Ne jamais committer un token en clair.
- **`folder.bru`** : définit l'auth héritée et la doc d'un sous-dossier — privilégier l'héritage plutôt que de répéter `auth:bearer` dans chaque requête.
- **Séquence (`seq`)** : numéroter les requêtes dans `meta` pour garder un ordre logique dans la sidebar Bruno.
- **Fichiers ignorés** : `.DS_Store`, `node_modules/`, `.vscode/`, `.claude/`, `.ralph-loop.local.md` (voir `.gitignore`).

## Migration depuis les anciens repos

Ce dépôt remplace les 7 repos individuels qui se trouvaient sous `~/Sites/bruno/{appgenerator,centris,elasticsearch,mlsconnect,monitoring,shareimmo,storeimmo}`. Le contenu a été copié à l'identique (vérifié byte-pour-byte). Une fois la migration validée par tous les contributeurs, les anciens repos peuvent être archivés.
