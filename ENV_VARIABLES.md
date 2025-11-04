# 🔧 Variables d'Environnement

Toutes les variables configurables de l'application.

## 📋 Variables Disponibles

### ✅ **VITE_SPOTIFY_CLIENT_ID** (Obligatoire)

Votre Client ID Spotify.

**Comment l'obtenir :**
1. https://developer.spotify.com/dashboard
2. Ouvrez votre app
3. Copiez le Client ID

**Exemple :**
```env
VITE_SPOTIFY_CLIENT_ID=abc123def456789...
```

---

### 🌐 **VITE_PRODUCTION_URL** (Obligatoire pour production)

URL de votre site en production (GitHub Pages, Netlify, Vercel, etc.)

**Exemple :**
```env
VITE_PRODUCTION_URL=https://jillpouchain.github.io/cassette-tape/
```

**Si vous forkez le projet**, remplacez par votre propre URL :
```env
VITE_PRODUCTION_URL=https://yourusername.github.io/your-repo-name/
```

**⚠️ Important :** 
- Cette URL doit correspondre **EXACTEMENT** à une Redirect URI dans votre Spotify App Settings !
- Cette variable est **obligatoire pour le déploiement en production**
- En local (localhost), elle n'est pas nécessaire

---

### 🎵 **VITE_SPOTIFY_DEFAULT_TRACK** (Optionnel)

Track Spotify jouée par défaut au démarrage.

**Par défaut :** `spotify:track:3n3Ppam7vgaVa1iaRUc9Lp` (The Weeknd - Blinding Lights)

**Comment trouver l'URI d'une track :**
1. Ouvrez Spotify (app ou web)
2. Trouvez une chanson
3. Clic droit → Partager → Copier le lien
4. Vous obtenez : `https://open.spotify.com/track/ABC123DEF456...`
5. L'URI est : `spotify:track:ABC123DEF456...`

**Exemple :**
```env
VITE_SPOTIFY_DEFAULT_TRACK=spotify:track:7qiZfU4dY1lWllzX7mPBI
```

---

## 📄 Exemple de fichier `.env` complet

### Configuration minimale (développement local)

```env
VITE_SPOTIFY_CLIENT_ID=abc123def456789
```

### Configuration pour production

```env
# Spotify Client ID (obligatoire)
VITE_SPOTIFY_CLIENT_ID=abc123def456789

# URL de production (obligatoire pour déploiement)
VITE_PRODUCTION_URL=https://jillpouchain.github.io/cassette-tape/
```

### Configuration complète (avec options)

```env
# Spotify Client ID (obligatoire)
VITE_SPOTIFY_CLIENT_ID=abc123def456789

# URL de production (obligatoire pour déploiement)
VITE_PRODUCTION_URL=https://jillpouchain.github.io/cassette-tape/

# Track par défaut (optionnel)
VITE_SPOTIFY_DEFAULT_TRACK=spotify:track:7qiZfU4dY1lWllzX7mPBI
```

---

## 🔒 Sécurité

### ✅ Valeurs SÛRES à partager (dans le code) :

- Valeurs par défaut génériques
- URL d'exemples
- Track Spotify publiques (ce sont des IDs publics)

### ❌ Valeurs PRIVÉES (doivent rester dans `.env`) :

- Votre Client ID Spotify (spécifique à votre compte)
- Votre URL de production personnelle

**Le fichier `.env` est dans `.gitignore` et ne sera JAMAIS committé sur GitHub !** ✅

---

## 🚀 Production (GitHub Pages)

Pour que les variables fonctionnent en production, ajoutez-les comme **GitHub Secrets** :

1. GitHub → Votre repo → Settings
2. Secrets and variables → Actions
3. New repository secret

**Secrets à créer (obligatoires) :**

```
Name: VITE_SPOTIFY_CLIENT_ID
Secret: votre_client_id

Name: VITE_PRODUCTION_URL
Secret: https://jillpouchain.github.io/cassette-tape/
```

**Secrets optionnels :**

```
Name: VITE_SPOTIFY_DEFAULT_TRACK
Secret: spotify:track:ABC123...
```

Le workflow GitHub Actions (`.github/workflows/deploy.yml`) les injectera automatiquement lors du build.

⚠️ **IMPORTANT** : Sans `VITE_PRODUCTION_URL`, l'authentification Spotify ne fonctionnera pas en production !

---

## 🔄 Modifier une variable

### En local :

1. Éditez `.env`
2. Modifiez la valeur
3. **Redémarrez le serveur** : `npm run dev`

### En production :

1. Modifiez le GitHub Secret
2. Re-pushez pour déclencher un rebuild :
   ```bash
   git commit --allow-empty -m "Update env vars"
   git push
   ```

---

## 🧪 Vérifier les variables

Dans la console du navigateur (F12) :

```javascript
// Vérifier toutes les variables
console.log({
  clientId: import.meta.env.VITE_SPOTIFY_CLIENT_ID,
  productionUrl: import.meta.env.VITE_PRODUCTION_URL,
  defaultTrack: import.meta.env.VITE_SPOTIFY_DEFAULT_TRACK
})
```

---

## 📚 Voir aussi

- [SECURE_SETUP.md](./SECURE_SETUP.md) - Configuration sécurisée complète
- [CREATE_ENV_FILE.md](./CREATE_ENV_FILE.md) - Comment créer le fichier .env
- [SPOTIFY_PKCE_SETUP.md](./SPOTIFY_PKCE_SETUP.md) - Configuration Spotify


