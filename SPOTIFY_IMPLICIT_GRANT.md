# 🔧 Erreur: unsupported_response_type

## Problème

URL d'erreur : `http://127.0.0.1:5173/cassette-tape/#error=unsupported_response_type`

Cette erreur signifie que votre app Spotify n'a pas l'**Implicit Grant Flow** activé.

## ✅ Solution : Activer l'Implicit Grant Flow

### Étape par étape :

1. **Allez sur le [Spotify Dashboard](https://developer.spotify.com/dashboard)**

2. **Ouvrez votre app** (cliquez dessus)

3. **Cliquez sur "Settings"** (bouton en haut à droite)

4. **Trouvez la section OAuth / Authorization**
   
   Cherchez une section qui dit :
   - "Which OAuth features you need?" ou
   - "Authorization flows" ou
   - "OAuth settings"

5. **Cochez/Activez :** ✅ **Implicit Grant**
   
   Vous devriez voir quelque chose comme :
   ```
   ☐ Authorization Code
   ☑️ Implicit Grant          ← COCHEZ CECI !
   ☐ Client Credentials
   ```

6. **Cliquez sur "Save"** en bas de la page

7. **Attendez quelques secondes** pour que Spotify mette à jour

8. **Testez à nouveau** votre app

## 📸 À quoi ça ressemble

Dans le Spotify Dashboard, section Settings, vous devriez voir :

```
OAuth Settings
--------------
Redirect URIs:
  http://127.0.0.1:5173
  https://jillpouchain.github.io/cassette-tape/

Which flows you need:
  ☐ Authorization Code
  ☑️ Implicit Grant             ← Activez ceci !
  ☐ Client Credentials
```

## 🔄 Après activation

1. Fermez tous les onglets de votre app
2. Ouvrez http://127.0.0.1:5173
3. Cliquez sur la cassette
4. Acceptez les permissions Spotify
5. Ça devrait fonctionner ! 🎉

## ⚠️ Si ça ne marche toujours pas

### Vérifiez ces 3 choses :

1. **Redirect URIs sont corrects** :
   ```
   http://127.0.0.1:5173
   https://jillpouchain.github.io/cassette-tape/
   ```

2. **Les scopes sont corrects** (dans Settings) :
   - `streaming`
   - `user-read-email`
   - `user-read-private`
   - `user-modify-playback-state`

3. **Le fichier .env contient le bon Client ID** :
   ```env
   VITE_SPOTIFY_CLIENT_ID=abc123def456...
   ```

## 💡 Pourquoi cette erreur ?

Le **Implicit Grant Flow** permet à votre app JavaScript (frontend) d'obtenir un token d'accès directement, sans passer par un serveur backend.

C'est le flow le plus simple pour les apps web comme votre cassette !

## 🆘 Toujours bloqué ?

Si après avoir activé "Implicit Grant" ça ne fonctionne toujours pas :

1. Vérifiez la console du navigateur (F12) pour d'autres erreurs
2. Essayez de supprimer et recréer l'app Spotify
3. Consultez [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)

