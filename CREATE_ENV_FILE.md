# 📝 Comment créer le fichier `.env`

## Option 1 : Via le terminal (Linux/Mac/Windows Git Bash)

```bash
# À la racine du projet cassette-tape
echo "VITE_SPOTIFY_CLIENT_ID=votre_client_id_ici" > .env
```

Remplacez `votre_client_id_ici` par votre vrai Client ID Spotify.

## Option 2 : Via VS Code / Éditeur

1. Ouvrez VS Code dans le dossier `cassette-tape`
2. Créez un nouveau fichier (Ctrl+N ou Cmd+N)
3. Collez cette ligne :
   ```
   VITE_SPOTIFY_CLIENT_ID=abc123def456...
   ```
4. **Enregistrez sous** (Ctrl+Shift+S ou Cmd+Shift+S)
5. Nom du fichier : `.env` (avec le point devant !)
6. Emplacement : À la racine du projet (à côté de `package.json`)

## Option 3 : Via l'explorateur de fichiers

### Windows :
1. Ouvrez le dossier `cassette-tape`
2. Clic droit → Nouveau → Document texte
3. Nommez-le `.env` (Windows va dire "sans extension de fichier", acceptez)
4. Ouvrez-le avec Notepad
5. Écrivez : `VITE_SPOTIFY_CLIENT_ID=votre_client_id`
6. Enregistrez

### Mac :
1. Ouvrez le dossier `cassette-tape` dans Finder
2. Cmd+Shift+. pour afficher les fichiers cachés
3. Clic droit → Nouveau document
4. Nommez-le `.env`
5. Ouvrez-le avec TextEdit
6. Écrivez : `VITE_SPOTIFY_CLIENT_ID=votre_client_id`
7. Enregistrez

## ✅ Vérification

Le fichier `.env` doit :
- Être à la **racine du projet** (à côté de `package.json`)
- Contenir **UNE ligne** : `VITE_SPOTIFY_CLIENT_ID=abc123...`
- Ne PAS avoir d'extension (pas `.env.txt`)

## 🎯 Structure finale

```
cassette-tape/
├── .env                    ← Votre fichier (ignoré par git)
├── .gitignore
├── package.json
├── src/
│   └── App.vue
└── ...
```

## 🔒 Sécurité

Le fichier `.env` est déjà listé dans `.gitignore`, donc Git ne le verra JAMAIS :

```gitignore
# .gitignore
.env
.env.local
.env.production
```

Vous pouvez vérifier avec :
```bash
git status
```

Le fichier `.env` ne devrait PAS apparaître ! ✅

## 🚀 Lancer l'app

```bash
npm run dev
```

Si tout est bon, vous verrez votre cassette sur http://localhost:5173 !

