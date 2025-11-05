# 🚀 Guide de Démarrage Rapide

## 📦 Installation (30 secondes)

```bash
# 1. Cloner le projet
git clone https://github.com/jillpouchain/cassette-tape.git
cd cassette-tape

# 2. Installer les dépendances
npm install

# 3. Lancer l'application
npm run dev
```

## 🎵 Ajouter votre musique (2 minutes)

### Étape 1 : Ajouter votre fichier MP3

Placez votre fichier MP3 dans le dossier `/public/musique/`

Exemple :
```
public/musique/
├── ma-super-chanson.mp3  ← Votre fichier ici
├── config.json
└── README.md
```

### Étape 2 : Mettre à jour la configuration

Éditez `/public/musique/config.json` :

```json
[
  {
    "id": "ma-chanson",
    "nom": "Ma Super Chanson - Mon Artiste Préféré",
    "duree": 210,
    "fichier": "ma-super-chanson.mp3"
  }
]
```

**Comment trouver la durée ?**
- **Windows** : Clic droit → Propriétés → Détails
- **Mac** : Clic droit → Informations → Durée
- **VLC** : Ouvrir le fichier et regarder la durée

### Étape 3 : Rafraîchir la page

Rechargez http://localhost:5173 et **c'est tout !** 🎉

## 🎮 Utilisation

- **Cliquer sur la cassette** → Play/Pause
- **Observer les bobines** → Elles tournent en sync avec la musique !
- **Cliquer sur "MIXTAPE 2025"** → Éditer le titre

## 📱 Sur mobile

L'application s'adapte automatiquement et tourne à 90° pour une meilleure expérience !

## 🐛 Problèmes courants

### La musique ne se lance pas

✅ **Solution** : Vérifiez que :
1. Le fichier MP3 est bien dans `/public/musique/`
2. Le nom du fichier dans `config.json` correspond exactement
3. La durée est en **secondes** (pas en minutes)

### Les bobines ne bougent pas correctement

✅ **Solution** : Vérifiez que la durée dans `config.json` est correcte (en secondes).

### Erreur 404 sur le fichier audio

✅ **Solution** : Le chemin dans `config.json` doit être juste le nom du fichier, pas le chemin complet.

**Bon :** `"fichier": "chanson.mp3"`  
**Mauvais :** `"fichier": "/public/musique/chanson.mp3"`

## 🎨 Personnalisation

### Changer le volume

Éditez `src/App.vue`, ligne 44 :
```typescript
audio.volume = 0.7  // 0.0 = muet, 1.0 = max
```

### Changer la couleur des bobines

Éditez `src/App.vue`, cherchez `.reel-center` dans le CSS et modifiez les couleurs du gradient.

## 🚢 Déployer

```bash
npm run build
```

Le dossier `dist/` contiendra votre application prête à être déployée !

---

**Besoin d'aide ?** → Consultez le [README.md](./README.md) complet !

