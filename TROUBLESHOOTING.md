# 🔧 Résolution de Problèmes

## ❌ Erreur "unsupported_response_type" ⭐ FRÉQUENT

### Problème
L'URL change en : `http://127.0.0.1:5173/cassette-tape/#error=unsupported_response_type`

### Cause
L'**Implicit Grant Flow** n'est pas activé dans votre app Spotify.

### ✅ Solution

**Dans le Spotify Dashboard :**

1. Ouvrez votre app sur https://developer.spotify.com/dashboard
2. Cliquez sur **Settings**
3. Trouvez la section **"OAuth"** ou **"Authorization flows"**
4. **Cochez** : ✅ **Implicit Grant**
5. Cliquez sur **Save**
6. Testez à nouveau

📖 Guide détaillé : [SPOTIFY_IMPLICIT_GRANT.md](./SPOTIFY_IMPLICIT_GRANT.md)

---

## ❌ Spotify refuse "http://localhost:5173" (not secure)

### Problème
Quand vous essayez d'ajouter `http://localhost:5173` dans les Redirect URIs de Spotify, vous obtenez une erreur "not secure".

### ✅ Solution 1 : Utiliser l'adresse IP (Recommandé)

Au lieu de `http://localhost:5173`, utilisez :
```
http://127.0.0.1:5173
```

**Pourquoi ça marche ?**
- `127.0.0.1` et `localhost` pointent vers la même chose (votre machine)
- Mais Spotify accepte mieux l'adresse IP en HTTP

**Le code a déjà été modifié pour utiliser `127.0.0.1` automatiquement !** ✅

### ✅ Solution 2 : Utiliser HTTPS en local

Si même `127.0.0.1` ne fonctionne pas, configurez HTTPS pour votre serveur local.

#### Étape 1 : Installer le plugin

```bash
npm install --save-dev @vitejs/plugin-basic-ssl
```

#### Étape 2 : Modifier `vite.config.ts`

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import basicSsl from '@vitejs/plugin-basic-ssl'

export default defineConfig({
  base: '/cassette-tape/',
  plugins: [
    vue(),
    basicSsl() // Active HTTPS
  ],
  server: {
    https: true
  }
})
```

#### Étape 3 : Mettre à jour les URIs

Dans Spotify Dashboard, utilisez :
```
https://localhost:5173
https://jillpouchain.github.io/cassette-tape/
```

Dans `src/App.vue`, ligne 22, changez :
```typescript
return 'https://localhost:5173'  // ou https://127.0.0.1:5173
```

#### Étape 4 : Lancer avec HTTPS

```bash
npm run dev
```

Ouvrez : `https://localhost:5173` (notez le **s** dans https)

⚠️ Vous verrez un avertissement de sécurité (certificat auto-signé), acceptez-le pour le développement.

---

## ❌ "Configuration Spotify manquante"

### Problème
Message : "Configuration Spotify manquante. Consultez le fichier SPOTIFY_SETUP.md"

### Cause
Le fichier `.env` n'existe pas ou est mal configuré.

### ✅ Solution

1. Créez le fichier `.env` à la racine du projet (à côté de `package.json`)
2. Ajoutez votre Client ID :
   ```env
   VITE_SPOTIFY_CLIENT_ID=abc123def456...
   ```
3. Redémarrez le serveur :
   ```bash
   npm run dev
   ```

📖 Voir [CREATE_ENV_FILE.md](./CREATE_ENV_FILE.md) pour un guide détaillé.

---

## ❌ "Spotify Premium requis"

### Problème
Message : "Spotify Premium requis pour utiliser ce player"

### Cause
Le Spotify Web Playback SDK ne fonctionne qu'avec des comptes Premium.

### ✅ Solutions

**Option A** : Utiliser un compte Premium
- Si vous avez Premium, connectez-vous avec ce compte

**Option B** : Mode test (jusqu'à 25 utilisateurs)
1. Allez dans votre [Spotify Dashboard](https://developer.spotify.com/dashboard)
2. Ouvrez votre app
3. Allez dans **Users and Access**
4. Ajoutez des utilisateurs de test (max 25)
5. Ces utilisateurs pourront utiliser l'app même avec un compte gratuit

**Option C** : Alternative sans Spotify Premium
Si vous voulez juste tester l'interface, utilisez un fichier audio local au lieu de Spotify.

---

## ❌ Les bobines ne bougent pas

### Problème
La cassette est affichée mais les bobines ne tournent pas.

### Causes possibles

1. **Pas authentifié avec Spotify**
   - Vérifiez la console du navigateur (F12)
   - Vous devriez voir "✅ Spotify Player Ready"

2. **Track non jouée**
   - Cliquez sur la cassette pour démarrer la lecture
   - Vérifiez que le son sort de votre ordinateur

3. **Erreur JavaScript**
   - Ouvrez la console (F12)
   - Regardez s'il y a des erreurs en rouge

### ✅ Solution

Ouvrez la console du navigateur et tapez :
```javascript
console.log(import.meta.env.VITE_SPOTIFY_CLIENT_ID)
```

- Si c'est `undefined` → Votre `.env` n'est pas lu
- Si c'est votre ID → Le problème est ailleurs

---

## ❌ Erreur d'authentification après redirection

### Problème
Après avoir accepté les permissions Spotify, vous revenez sur l'app mais rien ne se passe.

### Cause
Les Redirect URIs ne correspondent pas exactement.

### ✅ Solution

1. Vérifiez dans le Spotify Dashboard que vous avez **exactement** :
   ```
   http://127.0.0.1:5173
   ```
   (pas de slash final `/`, pas de `localhost`)

2. Accédez à votre app via l'URL exacte :
   ```
   http://127.0.0.1:5173
   ```
   (pas `http://localhost:5173`)

---

## ❌ L'app ne fonctionne pas sur GitHub Pages

### Problème
L'app fonctionne en local mais pas sur `https://jillpouchain.github.io/cassette-tape/`

### Causes possibles

1. **GitHub Secret non configuré**
   - Allez dans Settings → Secrets → Actions
   - Vérifiez que `VITE_SPOTIFY_CLIENT_ID` existe

2. **Redirect URI GitHub Pages manquante**
   - Dans Spotify Dashboard, ajoutez :
     ```
     https://jillpouchain.github.io/cassette-tape/
     ```
   - ⚠️ Avec le slash final `/` !

3. **Workflow GitHub Actions pas exécuté**
   - Allez dans l'onglet **Actions** de votre repo
   - Vérifiez qu'un workflow a bien été exécuté
   - Si erreur, regardez les logs

### ✅ Solution

Re-pusher pour déclencher un nouveau build :
```bash
git commit --allow-empty -m "Trigger rebuild"
git push
```

---

## ❌ La cassette ne s'affiche pas en mobile

### Problème
Sur mobile, la cassette n'apparaît pas ou est coupée.

### ✅ Solution

1. Videz le cache du navigateur mobile
2. Rechargez la page (pull to refresh)
3. Essayez en mode paysage ET portrait

---

## 📝 Besoin d'aide ?

Si votre problème n'est pas listé ici :

1. Ouvrez la console du navigateur (F12)
2. Regardez les erreurs en rouge
3. Vérifiez que tous les fichiers de configuration existent :
   - ✅ `.env` (en local)
   - ✅ Secret GitHub (en prod)
   - ✅ Redirect URIs dans Spotify

4. Consultez les guides :
   - [SECURE_SETUP.md](./SECURE_SETUP.md)
   - [CREATE_ENV_FILE.md](./CREATE_ENV_FILE.md)
   - [SPOTIFY_SETUP.md](./SPOTIFY_SETUP.md)

