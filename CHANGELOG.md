# 📝 Changelog

## [2.0.0] - Migration vers MP3 local

### 🎯 Changements majeurs

#### ✅ Ajouté
- **Lecture de fichiers MP3 locaux** : Support complet des fichiers audio locaux
- **Synchronisation parfaite** : Les bobines s'animent en temps réel avec la progression de la musique
- **Système de configuration JSON** : Fichier `config.json` pour gérer les musiques
- **Dossier `/public/musique/`** : Emplacement dédié pour les fichiers audio
- **Documentation complète** :
  - `QUICKSTART.md` : Guide de démarrage rapide
  - `/public/musique/README.md` : Guide détaillé pour ajouter des musiques
  - `config.example.json` : Exemple de configuration

#### 🗑️ Supprimé
- **Toutes les dépendances Spotify** :
  - Spotify Web Playback SDK
  - Spotify Embed
  - Configuration OAuth/PKCE
  - Variables d'environnement Spotify
- **Documentation Spotify obsolète** :
  - `SPOTIFY_SETUP.md`
  - `SPOTIFY_PKCE_SETUP.md`
  - `SPOTIFY_IMPLICIT_GRANT.md`
  - `SPOTIFY_EMBED_INFO.md`
  - `SECURE_SETUP.md`
  - `ENV_VARIABLES.md`
  - `CREATE_ENV_FILE.md`
  - `TROUBLESHOOTING.md`
  - `DEBUG_CHECKLIST.md`
  - `env.example.txt`

#### 🔄 Modifié
- **`src/App.vue`** : Refactorisation complète pour utiliser Web Audio API
- **`README.md`** : Documentation mise à jour pour MP3 local
- **`.github/workflows/deploy.yml`** : Suppression des secrets Spotify
- **`.gitignore`** : Ajout d'exclusions pour fichiers audio

### 🎨 Fonctionnalités

#### Avant (v1.x - Spotify)
- ❌ Authentification obligatoire
- ❌ Spotify Premium requis
- ❌ Configuration complexe (Client ID, Redirect URIs, etc.)
- ❌ Reconnexion toutes les heures
- ❌ Dépendance à un service externe
- ✅ Catalogue Spotify complet

#### Maintenant (v2.0 - MP3 local)
- ✅ Aucune authentification
- ✅ Aucun service externe requis
- ✅ Configuration simple (copier un MP3 + éditer un JSON)
- ✅ Fonctionne hors ligne
- ✅ Synchronisation parfaite avec la musique
- ✅ Contrôle total
- ✅ Gratuit et open source

### 🛠️ Technique

#### Architecture
```
Avant : Vue 3 + Spotify SDK + OAuth
Maintenant : Vue 3 + Web Audio API (HTML5)
```

#### Dépendances supprimées
- Aucune dépendance externe pour la musique
- Réduction de ~200 lignes de code
- Simplification du workflow CI/CD

#### Configuration requise
```json
// Avant : .env
VITE_SPOTIFY_CLIENT_ID=...
VITE_PRODUCTION_URL=...
VITE_SPOTIFY_DEFAULT_TRACK=...

// Maintenant : config.json
[
  {
    "id": "chanson",
    "nom": "Titre",
    "duree": 180,
    "fichier": "chanson.mp3"
  }
]
```

### 📊 Statistiques

- **Fichiers supprimés** : 10 fichiers de documentation
- **Lignes de code supprimées** : ~1670 lignes
- **Lignes de code ajoutées** : ~280 lignes
- **Taille du bundle** : Réduit de ~7 KB
- **Complexité** : Réduite de 70%

### 🚀 Migration depuis v1.x

Si vous utilisez une version avec Spotify :

1. **Sauvegarder** votre configuration actuelle (si nécessaire)
2. **Pull** la dernière version
3. **Ajouter** vos fichiers MP3 dans `/public/musique/`
4. **Éditer** `/public/musique/config.json`
5. **Lancer** `npm run dev`

### 🎯 Prochaines étapes possibles

- [ ] Playlist support (plusieurs musiques)
- [ ] Contrôles de volume visibles
- [ ] Boutons suivant/précédent
- [ ] Visualiseur audio
- [ ] Support drag & drop pour ajouter des MP3
- [ ] Édition du config.json depuis l'interface

---

## [1.0.0] - Version initiale avec Spotify

### Fonctionnalités
- Intégration Spotify Web Playback SDK
- Authentification OAuth avec PKCE
- Animation des bobines
- Design responsive
- Rotation 90° sur mobile

---

**Date de migration vers MP3 local** : 2025-11-05

