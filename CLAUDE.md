# CLAUDE.md

Guidance pour Claude Code lorsqu'il intervient sur ce dépôt.

## C'est quoi ce repo

Mono-repo qui regroupe **7 collections [Bruno](https://www.usebruno.com/)** (client API open-source). Chaque sous-dossier racine est une collection autonome avec son propre `bruno.json` et ses environnements.

| Dossier | Description rapide |
|---|---|
| `appgenerator/` | Génération d'apps internes (listglobally, pachamama) |
| `centris/` | API Centris Data Distribution (RESO, OData) — voir `centris/CLAUDE.md` |
| `elasticsearch/` | Requêtes Elasticsearch dev/preprod/prod |
| `mlsconnect/` | API MlsConnect (v1, v2, v3, OAuth, MCP, webhooks) |
| `monitoring/` | Health checks + emails + Slack + gateways + DNS — voir `monitoring/CLAUDE.md` |
| `shareimmo/` | API Shareimmo v2 + visibilités |
| `storeimmo/` | API Storeimmo + Immofacile |

Toujours lire le `CLAUDE.md` local d'une collection (s'il existe) avant d'y modifier des requêtes — il contient les conventions spécifiques (auth, environnements, ressources).

## Format `.bru`

- Texte structuré par blocs : `meta`, `get`/`post`/`put`/`delete`, `headers`, `auth:bearer`, `body:json`, `query`, `vars:pre-request`, `script:pre-request`, `script:post-response`, `tests`.
- `folder.bru` définit la métadonnée et l'auth héritée d'un sous-dossier.
- `{{variable}}` référence une variable de `<collection>/environments/<env>.bru`.
- L'ordre dans la sidebar Bruno est piloté par `seq` dans le bloc `meta` — préserver la séquence existante.

## Règles d'édition

- **Ne jamais committer de secrets** (tokens, clés API, mots de passe). Ils vivent en `vars:secret` dans les environnements et leurs valeurs restent dans l'UI Bruno côté utilisateur.
- **Une nouvelle requête** : créer un fichier `.bru` dans le bon sous-dossier, hériter l'auth via `folder.bru` quand c'est possible, attribuer un `seq` cohérent.
- **Renommer une requête** : modifier `name` dans `meta` ET le nom de fichier — les deux doivent rester synchronisés.
- **Ajouter une collection** : créer un dossier racine avec son `bruno.json` (`"version": "1"`, `"type": "collection"`, `"name": "<nom>"`) puis l'ajouter au tableau du `README.md`.
- **Modification fonctionnelle d'une API** (Centris, MlsConnect, etc.) : se référer à la doc officielle de l'API avant d'inventer des paramètres. Les conventions OData / REST / OAuth varient d'une collection à l'autre.

## Vérifications rapides

- Lister toutes les requêtes : `find <collection> -name '*.bru' -not -path '*/environments/*'`.
- Vérifier qu'un environnement compile : ouvrir la collection dans Bruno (pas d'outil CLI utilisé ici).
- Diff de migration depuis les anciens repos : `diff -rq /Users/jules/Sites/bruno/<collection> ./<collection> --exclude=.git --exclude=.vscode --exclude=.DS_Store`.

## Ce qui n'est PAS dans ce repo

- Pas de code applicatif Rails/JS — uniquement des collections d'API.
- Pas de tests automatisés exécutables en CI (les `tests` dans `.bru` tournent uniquement quand on lance la requête dans Bruno).
- Pas de `node_modules`, pas de build — c'est du texte versionné, point.

Le `Gemfile` à la racine sert uniquement au tooling local (`immosquare-cleaner` pour le hook PostToolUse de Claude Code) — ce n'est pas une app Ruby.
