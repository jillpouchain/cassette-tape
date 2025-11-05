# 🎵 Cassette Tape Player

Une cassette audio interactive rétro avec **lecture de fichiers MP3 locaux**, construite avec Vue 3 et TypeScript.

## ✨ Fonctionnalités

- **🎵 Lecture MP3 locale** : Jouez vos propres fichiers audio (aucun service externe requis)
- **🎯 Synchronisation en temps réel** : Les bobines s'animent parfaitement en sync avec la progression de la chanson
- **📼 Bobines animées** : Les bobines rouges grandissent et rétrécissent de façon réaliste pendant la lecture
- **⚙️ Mécanisme rotatif** : Les trous centraux tournent pendant la lecture
- **🏷️ Label intelligent** : Affiche automatiquement le nom de la chanson
- **📱 Design responsive** : Rotation automatique de 90° sur mobile pour une visualisation optimale
- **🎨 Design réaliste** : Apparence classique de cassette audio avec ombres et profondeur
- **🎧 Contrôle simple** : Clic sur la cassette = play/pause

## 🚀 Installation

### 1. Installer les dépendances
```bash
npm install
```

### 2. Ajouter vos fichiers MP3

1. Placez vos fichiers MP3 dans le dossier `/public/musique/`
2. Éditez le fichier `/public/musique/config.json` :

```json
[
  {
    "id": "chanson1",
    "nom": "Ma Chanson - Artiste",
    "duree": 180,
    "fichier": "ma-chanson.mp3"
  }
]
```

**Champs du JSON :**
- `id` : Identifiant unique (sans espaces)
- `nom` : Titre affiché sur la cassette
- `duree` : Durée en secondes (ex: 180 = 3 minutes)
- `fichier` : Nom du fichier MP3

📚 **Voir le guide complet** : `/public/musique/README.md`

### 3. Lancer en développement
```bash
npm run dev
```

### 4. Première utilisation

1. Ouvrir http://localhost:5173
2. Cliquer sur la cassette pour play/pause
3. Profiter de votre musique ! 🎵

### Build pour production
```bash
npm run build
```

### Prévisualiser le build de production
```bash
npm run preview
```

## 📖 Utilisation

- **Play/Pause** : Cliquer n'importe où sur la cassette
- **Observer la magie** : Les bobines s'animent en parfaite synchronisation avec la chanson
- **Info de la chanson** : Le nom de la chanson s'affiche automatiquement sur le label
- **Éditer le label** : Cliquer sur le texte "MIXTAPE 2025" pour le personnaliser
- **Mobile** : La cassette tourne automatiquement à 90° pour une expérience optimale

## 🎼 Comment trouver la durée d'un fichier MP3

**Sur Windows :**
- Clic droit sur le fichier → Propriétés → Détails → Durée

**Sur Mac :**
- Clic droit sur le fichier → Obtenir des informations → Plus d'informations → Durée

**En ligne :**
- Ouvrir le fichier dans VLC ou un autre lecteur audio

## 🛠️ Tech Stack

- Vue 3 avec Composition API
- TypeScript
- Vite
- Web Audio API (HTML5 Audio)
- CSS avec styles scoped et design responsive

## 📁 Structure du projet

```
src/
├── App.vue      # Composant principal de la cassette
├── main.ts      # Point d'entrée de l'app
└── style.css    # Styles globaux

public/
└── musique/
    ├── config.json    # Configuration des musiques
    ├── README.md      # Guide détaillé
    └── *.mp3          # Vos fichiers audio
```

## 🎨 Personnalisation

### Changer la chanson par défaut

La première chanson dans `/public/musique/config.json` sera celle qui joue par défaut.

Pour changer l'ordre, modifiez simplement l'ordre dans le tableau JSON.

### Modifier le volume

Éditez `src/App.vue`, ligne 44 :
```typescript
audio.volume = 0.7  // Valeur entre 0.0 et 1.0
```

## 📜 Légalité

⚠️ **Important** : Assurez-vous d'avoir les droits pour utiliser les fichiers audio que vous ajoutez au projet. Utilisez uniquement des fichiers dont vous possédez les droits ou qui sont libres de droits.

## 🤝 Contribution

Les contributions sont les bienvenues ! N'hésitez pas à ouvrir une issue ou une pull request.

---

Fait avec ❤️ et nostalgie pour l'ère des cassettes
