# Déploiement statique sur Hostinger (offre mutualisée)

Hostinger mutualisé n'exécute pas Node.js : seuls des fichiers statiques (HTML/CSS/JS) peuvent être servis. Voici comment adapter un projet Vite/React/Tailwind avec éventuellement un backend Node/Express pour qu'il fonctionne entièrement en mode statique.

## 1. Isoler ou supprimer le backend
- **Supprime** les dossiers de backend (ex. `romuo-api`, `server`, `api`) du build déployé ou ignore-les lors de l'upload.
- Aucune route Express ne doit être requise : le site doit pouvoir fonctionner en ouvrant `index.html` directement depuis `public_html`.

## 2. Configurer Vite pour un build statique
- Dans `vite.config.js`, force un base path relatif pour que les assets soient trouvés quand le site est servi depuis `public_html` :

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  base: './', // indispensable pour un hébergement statique type Hostinger
  plugins: [react()],
})
```

- Si vous utilisez TypeScript, conservez la même `base`.
- Évite les dépendances server-side (fs, path, process.env côté client, etc.).

## 3. Neutraliser les appels API locaux
- Remplace tout `fetch('/api/...')` ou `axios.get('/api/...')` par un **endpoint distant** hébergé ailleurs, par exemple :

```ts
const API_BASE = 'https://votre-api-hebergee-ailleurs.example.com'
fetch(`${API_BASE}/resource`)
```

- Si l'API est optionnelle, protège les appels avec des garde-fous (ex.: vérifier que la variable `API_BASE` est définie) pour éviter les erreurs en production.

## 4. Scripts npm pour un build statique
Ajoutez/complétez les scripts suivants dans `package.json` :

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

Ensuite :

```bash
npm install
npm run build
```

Le dossier `dist/` généré contiendra uniquement les fichiers statiques à uploader.

## 5. Vérifier `index.html` et les assets
- Vérifie que les chemins d'assets (fonts, images, CSS) sont **relatifs**. Avec `base: './'`, Vite génère des chemins comme `./assets/...` qui fonctionnent sur Hostinger.
- Si vous aviez mis des chemins absolus (ex. `/assets/logo.svg`), remplacez-les par des chemins relatifs ou par le helper Vite `new URL('./assets/logo.svg', import.meta.url)` dans le code React.

## 6. Upload vers Hostinger
- Exécute `npm run build` en local ou en CI pour produire `dist/`.
- Uploade le contenu de `dist/` (les fichiers et sous-dossiers) dans `public_html/` via FTP ou le gestionnaire de fichiers Hostinger.
- Si vous utilisez des routes client-side (React Router), activez la réécriture par défaut de Hostinger en ajoutant un `.htaccess` simple dans `public_html` :

```
RewriteEngine On
RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.html [L]
```

## 7. Post-déploiement
- Teste le site via l'URL publique pour vérifier le chargement des assets.
- Contrôle la console navigateur pour s'assurer qu'aucun appel API local n'est tenté.
- Ajoute un monitoring basique (ex. UptimeRobot) si l'app dépend d'une API externe.

