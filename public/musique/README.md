# 🎵 Dossier Musique

## Comment ajouter vos fichiers MP3

### 1. Ajouter votre fichier MP3

Placez vos fichiers MP3 dans ce dossier : `/public/musique/`

Exemple :
```
public/
└── musique/
    ├── config.json
    ├── ma-chanson.mp3
    ├── ma-chanson-2.mp3
    └── README.md
```

### 2. Mettre à jour la configuration

Éditez le fichier `config.json` dans ce dossier et ajoutez les informations de votre musique :

```json
[
  {
    "id": "chanson1",
    "nom": "Ma Chanson - Artiste",
    "duree": 180,
    "fichier": "ma-chanson.mp3"
  },
  {
    "id": "chanson2",
    "nom": "Une Autre Chanson - Artiste 2",
    "duree": 240,
    "fichier": "ma-chanson-2.mp3"
  }
]
```

### Champs du JSON

- **`id`** : Identifiant unique pour la chanson (sans espaces)
- **`nom`** : Nom affiché sur la cassette (ex: "Titre - Artiste")
- **`duree`** : Durée en secondes (ex: 180 = 3 minutes)
- **`fichier`** : Nom du fichier MP3 (doit être dans ce dossier)

### 3. Comment trouver la durée d'un MP3

**Sur Windows :**
- Clic droit sur le fichier → Propriétés → Détails → Durée

**Sur Mac :**
- Clic droit sur le fichier → Obtenir des informations → Plus d'informations → Durée

**En ligne :**
- Ouvrir le fichier dans VLC ou un lecteur audio

### 4. Sélectionner la musique par défaut

La première chanson du tableau dans `config.json` sera celle qui joue par défaut.

Pour changer l'ordre, modifiez simplement l'ordre dans le JSON.

---

**Note :** Assurez-vous d'avoir les droits pour utiliser les fichiers audio ! 🎵

