# 🎵 Configuration Spotify

## ⚠️ Configuration Sécurisée

**IMPORTANT** : N'ajoutez JAMAIS votre Client ID directement dans le code !

👉 **Suivez le guide complet** : [SECURE_SETUP.md](./SECURE_SETUP.md)

Ce guide vous explique comment :
- 🔒 Garder votre Client ID privé (fichier `.env`)
- 🚀 Déployer sur GitHub Pages en toute sécurité (GitHub Secrets)
- ✅ Faire fonctionner l'app en local ET en production

---

## Résumé rapide

### Étape 1 : Récupérer votre Client ID

1. Allez sur votre app dans le [Spotify Dashboard](https://developer.spotify.com/dashboard)
2. Cliquez sur votre app "Cassette Tape"
3. Copiez le **Client ID** affiché

### Étape 1.5 : ⚠️ IMPORTANT - Activer Implicit Grant

**Avant de continuer, vous DEVEZ activer l'Implicit Grant Flow :**

1. Dans votre app Spotify Dashboard, cliquez sur **Settings**
2. Trouvez la section **"OAuth"** ou **"Authorization flows"**
3. **COCHEZ** : ✅ **Implicit Grant**
4. Cliquez sur **Save**

⚠️ **Sans cette étape, vous obtiendrez l'erreur `unsupported_response_type` !**

📖 Guide détaillé : [SPOTIFY_IMPLICIT_GRANT.md](./SPOTIFY_IMPLICIT_GRANT.md)

### Étape 2 : Créer le fichier `.env`

À la racine du projet, créez un fichier `.env` :

```env
VITE_SPOTIFY_CLIENT_ID=votre_client_id_ici
```

**Le fichier `.env` est déjà dans `.gitignore` et ne sera JAMAIS committé sur GitHub ✅**

## Étape 3 : Changer la track par défaut (optionnel)

À la ligne 17, vous pouvez changer la track Spotify jouée par défaut :

```typescript
const defaultTrackUri = ref('spotify:track:3n3Ppam7vgaVa1iaRUc9Lp')
```

### Comment trouver l'URI d'une track Spotify :

1. Ouvrez Spotify (web ou app)
2. Trouvez une chanson que vous aimez
3. Clic droit → "Partager" → "Copier le lien de la chanson"
4. Vous obtenez : `https://open.spotify.com/track/3n3Ppam7vgaVa1iaRUc9Lp?si=...`
5. L'URI est : `spotify:track:3n3Ppam7vgaVa1iaRUc9Lp`

## Étape 4 : Tester

1. Lancez l'app : `npm run dev`
2. Ouvrez http://localhost:5173
3. Cliquez sur la cassette
4. Vous serez redirigé vers Spotify pour vous authentifier
5. Acceptez les permissions
6. Vous revenez sur l'app et la musique démarre ! 🎉

## ⚠️ Important

- **Spotify Premium requis** pour que ça fonctionne
- Les bobines se synchronisent automatiquement avec la progression de la musique
- Le titre de la track s'affiche sur l'étiquette de la cassette

## 🎨 Fonctionnalités

✅ **Lecture/Pause** : Cliquez sur la cassette
✅ **Synchronisation** : Les bobines suivent la progression réelle de la musique
✅ **Affichage du titre** : Le nom de la track et l'artiste apparaissent sur l'étiquette
✅ **Rotation des centres** : Les trous centraux tournent pendant la lecture
✅ **Responsive** : Fonctionne sur mobile (rotation 90°)

## 🔧 Déploiement sur GitHub Pages

Le code est déjà configuré pour fonctionner sur :
- `http://localhost:5173` (développement)
- `https://jillpouchain.github.io/cassette-tape/` (production)

Assurez-vous que ces deux URIs sont bien dans les **Redirect URIs** de votre app Spotify !

