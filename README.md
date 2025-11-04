# 🎵 Cassette Tape Player

An interactive retro cassette tape player built with Vue 3 and TypeScript.

## Features

- **Interactive playback**: Click the cassette to play/pause
- **Animated tape reels**: Red reels that grow and shrink realistically as the tape plays
- **Spinning mechanism**: Rotating center holes when playing
- **Editable label**: Click the title band to customize your mixtape name
- **Responsive design**: Automatically rotates 90° on mobile devices for optimal viewing
- **Realistic design**: Classic cassette tape appearance with proper shadows and depth

## Getting Started

### Install dependencies
```bash
npm install
```

### Development
```bash
npm run dev
```

### Build for production
```bash
npm run build
```

### Preview production build
```bash
npm run preview
```

## Usage

- **Play/Pause**: Click anywhere on the cassette body
- **Edit Title**: Click on the label to type your custom title (max 30 characters)
- **Mobile**: View in portrait mode - the cassette will automatically rotate 90° for the best experience

## Tech Stack

- Vue 3 with Composition API
- TypeScript
- Vite
- CSS with scoped styles

## Project Structure

```
src/
├── App.vue      # Main cassette component
├── main.ts      # App entry point
└── style.css    # Global styles
```

---

Made with ❤️ and nostalgia for the cassette era
