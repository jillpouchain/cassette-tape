# ✅ Configuration Spotify avec PKCE (2024)

Spotify a retiré l'Implicit Grant Flow. On utilise maintenant **Authorization Code with PKCE** qui est plus sécurisé !

## 🎯 Configuration Simple

### Étape 1 : Dans Spotify Dashboard

1. Allez sur https://developer.spotify.com/dashboard
2. Ouvrez votre app (ou créez-en une)
3. Cliquez sur **"Settings"**

### Étape 2 : Redirect URIs

Ajoutez exactement ces URIs :

```
http://127.0.0.1:5173
https://jillpouchain.github.io/cassette-tape/
```

⚠️ **Sans slash final** pour la première !

### Étape 3 : APIs / SDKs

Cochez :
```
☑️ Web API
☑️ Web Playback SDK
```

### Étape 4 : AUCUNE autre configuration !

**Bonne nouvelle** : Avec PKCE, vous n'avez PAS besoin de cocher "Implicit Grant" !

PKCE fonctionne automatiquement et est activé par défaut pour toutes les apps Spotify.

### Étape 5 : Copiez votre Client ID

En haut de la page, copiez le **Client ID**

### Étape 6 : Créez le fichier `.env`

À la racine du projet, créez un fichier `.env` avec :

**Pour le développement local** (minimum) :
```env
VITE_SPOTIFY_CLIENT_ID=votre_client_id
```

**Pour la production** (obligatoire pour GitHub Pages) :
```env
VITE_SPOTIFY_CLIENT_ID=votre_client_id
VITE_PRODUCTION_URL=https://jillpouchain.github.io/cassette-tape/
```

⚠️ **Important** : Remplacez `jillpouchain` et `cassette-tape` par vos propres valeurs si vous avez forké le projet !

### Étape 7 : Lancez l'app

```bash
npm run dev
```

Ouvrez : http://127.0.0.1:5173

### Étape 8 : Testez !

1. Cliquez sur la cassette
2. Acceptez les permissions Spotify
3. Vous revenez sur l'app
4. La musique démarre ! 🎉

---

## 🔍 Comment ça fonctionne (PKCE)

### Différence avec Implicit Grant :

**Avant (Implicit Grant)** :
```
App → Spotify → Retour avec #access_token=...
```

**Maintenant (PKCE)** :
```
App → Génère code_verifier
    → Génère code_challenge
    → Spotify (avec challenge)
    → Retour avec ?code=...
    → Échange code + verifier contre token
    → ✅ Token obtenu !
```

### Avantages de PKCE :

✅ Plus sécurisé (le token ne passe jamais dans l'URL)
✅ Pas besoin de Client Secret
✅ Fonctionne en frontend pur
✅ Recommandé par Spotify en 2024

---

## ❌ Erreurs courantes

### "invalid_grant"

**Cause** : Le code a expiré (10 minutes max)

**Solution** : Réessayez, cliquez à nouveau sur la cassette

### "redirect_uri_mismatch"

**Cause** : Les URIs ne correspondent pas exactement

**Solution** : 
1. Vérifiez : `http://127.0.0.1:5173` (sans /)
2. Accédez via : `http://127.0.0.1:5173` (pas localhost)

### "unsupported_response_type"

**Cause** : Vous êtes peut-être encore en Implicit Grant dans le code

**Solution** : Le code a été mis à jour pour PKCE, redémarrez :
```bash
npm run dev
```

---

## ✅ Checklist finale

Avant de tester, vérifiez :

- [ ] Client ID copié dans `.env`
- [ ] Redirect URIs : `http://127.0.0.1:5173`
- [ ] Web API et Web Playback SDK cochés
- [ ] Serveur redémarré : `npm run dev`
- [ ] URL d'accès : `http://127.0.0.1:5173`
- [ ] Spotify Premium (obligatoire)

---

## 🎉 C'est tout !

Avec PKCE, c'est encore plus simple qu'avant. Pas besoin de configuration OAuth spéciale, ça marche out-of-the-box !

