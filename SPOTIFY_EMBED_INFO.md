# 🎵 Spotify Embed - Informations

## ✨ Ce qui a changé

Le projet utilise maintenant **Spotify Embed** au lieu de **Spotify Web Playback SDK**.

### Avantages

✅ **Aucune authentification requise** - Pas besoin de se connecter à Spotify !  
✅ **Pas de configuration complexe** - Plus besoin de créer une app Spotify  
✅ **Pas de Client ID** - Aucune clé API nécessaire  
✅ **Fonctionne immédiatement** - Juste cliquer et écouter  
✅ **Plus simple à maintenir** - Moins de code, moins de problèmes  

### Inconvénients

⚠️ **Contrôle limité** - L'animation des bandes est indépendante de la musique  
⚠️ **Pas de synchronisation parfaite** - Les bandes ne se synchronisent plus avec la durée de la chanson  
⚠️ **Design moins intégré** - Le player Spotify apparaît dans une iframe séparée  

## 🎯 Comment ça marche

1. **Cliquer sur la cassette** → Un player Spotify Embed apparaît
2. **Utiliser le player** → Contrôler la musique (play, pause, volume, etc.)
3. **L'animation continue** → Les bandes tournent indépendamment du player

## 🔧 Configuration

### Variables d'environnement

Seule **une variable optionnelle** est disponible :

```env
VITE_SPOTIFY_DEFAULT_TRACK=3n3Ppam7vgaVa1iaRUc9Lp
```

**Comment trouver un ID de track Spotify :**

1. Aller sur [Spotify Web](https://open.spotify.com/)
2. Trouver une chanson → Partager → Copier le lien
3. Extraire l'ID : `https://open.spotify.com/track/3n3Ppam7vgaVa1iaRUc9Lp`
4. L'ID est : `3n3Ppam7vgaVa1iaRUc9Lp`

### GitHub Secrets (optionnel)

Si vous voulez définir une track par défaut pour GitHub Pages :

1. Aller sur : `https://github.com/USERNAME/REPO/settings/secrets/actions`
2. Ajouter le secret : `VITE_SPOTIFY_DEFAULT_TRACK` avec l'ID de votre track

**Note :** Ce n'est PAS obligatoire ! Une track par défaut est déjà configurée.

## 🚀 Déploiement

Le déploiement sur GitHub Pages est maintenant **encore plus simple** :

1. Push votre code
2. GitHub Actions build automatiquement
3. C'est tout ! ✅

Plus besoin de :
- ❌ Configurer `VITE_SPOTIFY_CLIENT_ID`
- ❌ Configurer `VITE_PRODUCTION_URL`
- ❌ Créer une app Spotify
- ❌ Gérer les Redirect URIs
- ❌ S'authentifier

## 📱 Utilisation

### Desktop
- Cliquer sur la cassette pour ouvrir le player
- Utiliser les contrôles Spotify pour gérer la musique
- Cliquer sur ✕ pour fermer le player

### Mobile
- La cassette tourne automatiquement à 90°
- Cliquer pour ouvrir le player
- Le player s'adapte à la taille de l'écran

## 🎨 Personnalisation

### Changer le titre de la cassette

Cliquer directement sur "MIXTAPE 2025" et éditer le texte.

### Changer la track par défaut

Voir la section "Configuration" ci-dessus.

## 🔄 Pour revenir à l'ancienne version (avec authentification)

Si vous voulez revenir au système avec authentification complète :

```bash
git revert HEAD
```

Mais vous devrez alors reconfigurer :
- Client ID Spotify
- Redirect URIs
- Et vous devrez vous authentifier à chaque utilisation

---

**Profitez de votre cassette sans contraintes ! 🎵✨**

