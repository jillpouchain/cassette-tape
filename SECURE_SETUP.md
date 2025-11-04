# 🔒 Configuration Sécurisée avec Variables d'Environnement

Ce guide explique comment configurer votre app **SANS** mettre votre Client ID Spotify sur GitHub.

## 📁 Structure des fichiers

```
.env                    ← Votre Client ID (IGNORÉ par Git) ✅
env.example.txt         ← Template (committable sur Git)
.gitignore              ← Liste .env pour ne pas le committer
```

---

## 🏠 Configuration LOCALE (Développement)

### Étape 1 : Créer le fichier `.env`

À la racine du projet, créez un fichier nommé exactement `.env` (sans extension) :

```bash
# Dans le terminal, à la racine du projet
touch .env
```

Ou créez-le manuellement avec votre éditeur.

### Étape 2 : Ajouter votre Client ID

Ouvrez `.env` et ajoutez :

```env
VITE_SPOTIFY_CLIENT_ID=abc123def456789...votre_client_id
```

**⚠️ Remplacez par votre VRAI Client ID depuis le [Spotify Dashboard](https://developer.spotify.com/dashboard)**

### Étape 3 : Vérifier

Le fichier `.env` doit contenir UNE ligne :
```
VITE_SPOTIFY_CLIENT_ID=votre_vrai_client_id_ici
```

### Étape 4 : Tester

```bash
npm run dev
```

Ouvrez http://localhost:5173 et cliquez sur la cassette. Ça devrait fonctionner ! ✅

---

## 🚀 Configuration PRODUCTION (GitHub Pages)

Pour que votre app fonctionne sur GitHub Pages, vous devez configurer un **GitHub Secret**.

### Étape 1 : Aller dans les Settings du repo

1. Ouvrez votre repo sur GitHub : https://github.com/jillpouchain/cassette-tape
2. Cliquez sur **Settings** (en haut à droite)
3. Dans le menu de gauche, cliquez sur **Secrets and variables** → **Actions**

### Étape 2 : Créer un nouveau secret

1. Cliquez sur **New repository secret**
2. Name : `VITE_SPOTIFY_CLIENT_ID`
3. Secret : Collez votre Client ID Spotify
4. Cliquez sur **Add secret**

### ⚠️ Important : Redirect URIs Spotify

Dans votre app Spotify Dashboard, ajoutez ces URIs :

```
http://127.0.0.1:5173
https://jillpouchain.github.io/cassette-tape/
```

**Note** : Si Spotify refuse `http://localhost:5173` (non sécurisé), utilisez `http://127.0.0.1:5173` à la place. C'est exactement la même chose mais Spotify l'accepte mieux !

### Étape 3 : Créer un workflow GitHub Actions

Créez le fichier `.github/workflows/deploy.yml` :

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          
      - name: Install dependencies
        run: npm ci
        
      - name: Build with env variables
        env:
          VITE_SPOTIFY_CLIENT_ID: ${{ secrets.VITE_SPOTIFY_CLIENT_ID }}
        run: npm run build
        
      - name: Setup Pages
        uses: actions/configure-pages@v4
        
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: './dist'
          
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### Étape 4 : Activer GitHub Pages

1. Settings → Pages
2. Source : **GitHub Actions**
3. Save

### Étape 5 : Pusher et déployer

```bash
git add .
git commit -m "Add secure Spotify configuration"
git push
```

GitHub Actions va automatiquement :
1. Builder votre app avec le Client ID depuis les Secrets
2. Déployer sur GitHub Pages

🎉 Votre app sera disponible sur https://jillpouchain.github.io/cassette-tape/

---

## ✅ Vérification de Sécurité

### Ce qui EST sur GitHub :
- ✅ `env.example.txt` - Template vide
- ✅ `.gitignore` - Ignore les fichiers .env
- ✅ Code qui utilise `import.meta.env.VITE_SPOTIFY_CLIENT_ID`

### Ce qui N'EST PAS sur GitHub :
- ❌ `.env` - Votre fichier local avec le Client ID
- ❌ Le Client ID en dur dans le code

### Où est le Client ID ?
- 🏠 **Local** : Dans votre fichier `.env` (non committé)
- ☁️ **GitHub Pages** : Dans les GitHub Secrets (chiffré)

---

## 🔍 Dépannage

### Erreur "Configuration Spotify manquante"

**Cause** : Fichier `.env` manquant ou mal configuré

**Solution** :
1. Vérifiez que `.env` existe à la racine
2. Vérifiez le contenu : `VITE_SPOTIFY_CLIENT_ID=votre_id`
3. Redémarrez le serveur : `npm run dev`

### L'app ne fonctionne pas sur GitHub Pages

**Cause** : Secret GitHub non configuré

**Solution** :
1. Vérifiez Settings → Secrets → Actions
2. Le secret `VITE_SPOTIFY_CLIENT_ID` doit exister
3. Re-pusher pour déclencher le workflow

### Comment vérifier si la variable est chargée ?

Dans la console du navigateur :
```javascript
console.log(import.meta.env.VITE_SPOTIFY_CLIENT_ID)
```

⚠️ En production, ça affichera votre Client ID (c'est normal, ce n'est pas un secret critique).

---

## 💡 Pourquoi cette méthode ?

1. **Sécurité** : Votre Client ID n'est jamais dans le code committé
2. **Flexibilité** : Différents IDs pour dev/prod si besoin
3. **Collaboration** : D'autres devs peuvent cloner sans voir votre ID
4. **Best Practice** : Standard dans l'industrie

---

## 📚 Ressources

- [Vite Env Variables](https://vitejs.dev/guide/env-and-mode.html)
- [GitHub Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
- [Spotify Dashboard](https://developer.spotify.com/dashboard)

