# 🐛 Checklist de Debugging

Suivez ces étapes dans l'ordre pour identifier le problème.

## Étape 1 : Vérifier le fichier .env

### Commande
```bash
cat .env
```

### Résultat attendu
```
VITE_SPOTIFY_CLIENT_ID=abc123def456...
```

### ✅ Vérifications :
- [ ] Le fichier `.env` existe à la racine du projet
- [ ] Il contient `VITE_SPOTIFY_CLIENT_ID=...`
- [ ] Le Client ID a environ 32 caractères
- [ ] Pas d'espaces avant ou après le `=`
- [ ] Pas de guillemets autour de l'ID

### ❌ Si problème :
Créez/corrigez le fichier `.env` :
```bash
echo "VITE_SPOTIFY_CLIENT_ID=votre_vrai_client_id" > .env
```

---

## Étape 2 : Redémarrer le serveur

Après avoir créé/modifié `.env`, vous DEVEZ redémarrer :

```bash
# Arrêtez le serveur (Ctrl+C)
npm run dev
```

---

## Étape 3 : Vérifier dans le navigateur

### Ouvrez la console (F12)

### Commande dans la console :
```javascript
console.log(import.meta.env.VITE_SPOTIFY_CLIENT_ID)
```

### Résultat attendu
```
abc123def456...votre_client_id
```

### ❌ Si vous voyez `undefined` :
1. Le fichier `.env` n'est pas lu
2. Redémarrez le serveur : `npm run dev`
3. Vérifiez que le fichier s'appelle exactement `.env` (pas `.env.txt`)

---

## Étape 4 : Vérifier l'URL

### Vous utilisez quelle URL ?

✅ CORRECT :
```
http://127.0.0.1:5173
```

❌ INCORRECT :
```
http://localhost:5173       (utilisez 127.0.0.1 à la place)
http://127.0.0.1:5173/      (pas de / final pour la racine)
```

---

## Étape 5 : Spotify Dashboard - Checklist complète

### Redirect URIs (Settings)
```
✅ http://127.0.0.1:5173
✅ https://jillpouchain.github.io/cassette-tape/
```

**Attention** : EXACTEMENT ces URLs, sans espace, sans / final pour la première !

### OAuth (Settings)
```
☑️ Implicit Grant              ← DOIT être coché !
```

### APIs/SDKs (lors de la création)
```
☑️ Web API
☑️ Web Playback SDK
```

---

## Étape 6 : Tester l'authentification

### Dans la console (F12), exécutez :

```javascript
// 1. Vérifier le Client ID
console.log('Client ID:', import.meta.env.VITE_SPOTIFY_CLIENT_ID)

// 2. Vérifier la redirect URI détectée
const getRedirectUri = () => {
  if (window.location.hostname === 'localhost' || window.location.hostname === '127.0.0.1') {
    return 'http://127.0.0.1:5173'
  }
  return 'https://jillpouchain.github.io/cassette-tape/'
}
console.log('Redirect URI:', getRedirectUri())

// 3. Construire l'URL d'auth
const clientId = import.meta.env.VITE_SPOTIFY_CLIENT_ID
const redirectUri = getRedirectUri()
const scopes = 'streaming user-read-email user-read-private user-modify-playback-state'
const authUrl = `https://accounts.spotify.com/authorize?client_id=${clientId}&response_type=token&redirect_uri=${encodeURIComponent(redirectUri)}&scope=${encodeURIComponent(scopes)}`

console.log('Auth URL:', authUrl)
```

### Copiez l'URL complète et envoyez-la moi

---

## Étape 7 : Erreurs courantes et solutions

### ❌ "Configuration Spotify manquante"
**Cause** : `.env` n'existe pas ou est mal configuré
**Solution** : Voir Étape 1

### ❌ `#error=unsupported_response_type`
**Cause** : Implicit Grant pas activé
**Solution** :
1. Spotify Dashboard → Settings
2. Cocher "Implicit Grant"
3. Save

### ❌ `#error=redirect_uri_mismatch`
**Cause** : Les Redirect URIs ne correspondent pas exactement
**Solution** :
1. Vérifiez dans Spotify que c'est EXACTEMENT : `http://127.0.0.1:5173`
2. Accédez à l'app via : `http://127.0.0.1:5173` (pas localhost)

### ❌ `#error=invalid_client`
**Cause** : Client ID incorrect
**Solution** :
1. Vérifiez votre Client ID dans le Spotify Dashboard
2. Vérifiez que c'est le même dans `.env`
3. Redémarrez le serveur

### ❌ Rien ne se passe
**Cause** : JavaScript bloqué ou erreur
**Solution** :
1. Ouvrez la console (F12)
2. Regardez les erreurs en rouge
3. Envoyez-moi le message d'erreur

---

## Étape 8 : Videz le cache

Parfois le cache du navigateur pose problème :

1. **Chrome/Edge** : Ctrl+Shift+Delete → Cocher "Cached images" → Clear
2. **Firefox** : Ctrl+Shift+Delete → Cocher "Cache" → Clear
3. Ou ouvrez en **Navigation privée** (Ctrl+Shift+N)

---

## Étape 9 : Informations à me fournir

Si ça ne marche toujours pas, donnez-moi :

1. **L'erreur dans l'URL** (après le `#`)
   ```
   Exemple : #error=unsupported_response_type
   ```

2. **Les erreurs dans la console** (F12 → onglet Console)
   ```
   Copiez les messages en rouge
   ```

3. **Résultat de cette commande dans la console** :
   ```javascript
   console.log(import.meta.env.VITE_SPOTIFY_CLIENT_ID)
   ```

4. **Capture d'écran de vos Redirect URIs** dans Spotify Settings

5. **Capture d'écran de OAuth settings** (Implicit Grant coché ?)

---

## ✅ Ça marche enfin ?

Vous devriez :
1. Être redirigé vers Spotify
2. Voir "Cassette Tape Player wants to..."
3. Cliquer sur "Agree"
4. Revenir sur votre app
5. Voir la console dire : "✅ Spotify Player Ready"
6. La musique commence !

---

## 🆘 Dernier recours

Si VRAIMENT rien ne marche, essayez de :

1. **Supprimer l'app Spotify** et en recréer une nouvelle
2. **Vérifier que vous avez Spotify Premium** (obligatoire)
3. **Essayer avec un autre navigateur** (Chrome, Firefox)
4. **Désactiver les extensions** qui bloquent les popups/redirections

