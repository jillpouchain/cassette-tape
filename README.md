# 🎵 Cassette Tape Player

An interactive retro cassette tape player with **Spotify Embed** built with Vue 3 and TypeScript.

## Features

- **🎧 Spotify Integration**: Play real music from Spotify with Embed Player (no authentication needed!)
- **🎵 Interactive playback**: Click the cassette to reveal the Spotify player
- **📼 Animated tape reels**: Red reels that grow and shrink realistically
- **⚙️ Spinning mechanism**: Rotating center holes when playing
- **🏷️ Editable label**: Customize the cassette title
- **📱 Responsive design**: Automatically rotates 90° on mobile devices for optimal viewing
- **🎨 Realistic design**: Classic cassette tape appearance with proper shadows and depth
- **✨ No login required**: Uses Spotify Embed - just click and listen!

## Getting Started

### 1. Install dependencies
```bash
npm install
```

### 2. (Optional) Configure default Spotify track

**✨ No Spotify app or authentication required!**

By default, the cassette plays a demo track. To change it:

1. **Find a Spotify track ID:**
   - Go to [Spotify Web](https://open.spotify.com/)
   - Find any song and click "Share" → "Copy link"
   - Example link: `https://open.spotify.com/track/3n3Ppam7vgaVa1iaRUc9Lp`
   - Extract the ID: `3n3Ppam7vgaVa1iaRUc9Lp`

2. **Create a `.env` file** at the project root:
   ```env
   VITE_SPOTIFY_DEFAULT_TRACK=3n3Ppam7vgaVa1iaRUc9Lp
   ```

That's it! No Spotify app, no Client ID, no authentication needed! 🎉

### 3. Development
```bash
npm run dev
```

### 4. First Use

1. Open http://localhost:5173
2. Click on the cassette to reveal the Spotify player
3. Enjoy your music! 🎵 (No login required!)

### Build for production
```bash
npm run build
```

### Preview production build
```bash
npm run preview
```

## Usage

- **Click the cassette**: Opens the Spotify embed player
- **Watch the animation**: The tape reels spin and animate
- **Edit the label**: Click on the "MIXTAPE 2025" text to customize it
- **Mobile**: View in portrait or landscape - the cassette automatically rotates 90° for the best experience
- **Control music**: Use the Spotify embed player to play, pause, adjust volume, etc.

### Change the default track

Add to your `.env` file:
```env
VITE_SPOTIFY_DEFAULT_TRACK=3n3Ppam7vgaVa1iaRUc9Lp
```

**How to find a Spotify track ID:**
1. Open [Spotify Web](https://open.spotify.com/)
2. Find any song → Share → Copy song link
3. Extract the ID from the URL: `https://open.spotify.com/track/3n3Ppam7vgaVa1iaRUc9Lp`
4. The ID is: `3n3Ppam7vgaVa1iaRUc9Lp`

## Tech Stack

- Vue 3 with Composition API
- TypeScript
- Vite
- Spotify Embed Player (iframe)
- CSS with scoped styles and responsive design

## Project Structure

```
src/
├── App.vue      # Main cassette component
├── main.ts      # App entry point
└── style.css    # Global styles
```

---

Made with ❤️ and nostalgia for the cassette era
