# Clicom-31-12-2025

Ce dépôt est préparé pour accueillir un projet Vite/React. Hostinger (offre mutualisée Business) ne permet pas d'exécuter Node.js, mais il peut servir des fichiers statiques générés via `vite build`.

- Consultez `HOSTINGER_DEPLOYMENT.md` pour la checklist complète de conversion en mode statique (suppression du backend, configuration de `vite.config.js` avec `base: './'`, remplacement des appels API locaux, etc.).
- Après avoir ajouté le code front (par ex. dans `src/`), installez les dépendances avec `npm install`, puis générez les fichiers statiques avec `npm run build`. Uploadez ensuite le contenu du dossier `dist/` dans `public_html/` sur Hostinger.

