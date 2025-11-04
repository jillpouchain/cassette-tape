# 🎵 Cassette Tape Player

An interactive retro cassette tape player with **Spotify integration** built with Vue 3 and TypeScript.

## Features

- **🎧 Spotify Integration**: Play real music from Spotify with Web Playback SDK
- **🎯 Real-time Synchronization**: Tape reels animate perfectly in sync with the actual song progress
- **🎵 Interactive playback**: Click the cassette to play/pause
- **📼 Animated tape reels**: Red reels that grow and shrink realistically as the song plays
- **⚙️ Spinning mechanism**: Rotating center holes when playing
- **🏷️ Smart label**: Automatically displays the current track name and artist
- **📱 Responsive design**: Automatically rotates 90° on mobile devices for optimal viewing
- **🎨 Realistic design**: Classic cassette tape appearance with proper shadows and depth

## Getting Started

### 1. Install dependencies
```bash
npm install
```

### 2. Configure Spotify (Required)

**⚠️ Important**: Spotify Premium account required to play music.

#### 🔒 Secure Configuration (Recommended)

**DO NOT put your Client ID directly in the code!**

Follow the complete guide: **[SECURE_SETUP.md](./SECURE_SETUP.md)**

**Quick steps:**

1. Go to [Spotify Developer Dashboard](https://developer.spotify.com/dashboard)
2. Create/open your app and copy the **Client ID**
3. Add these **Redirect URIs** in your app settings:
   - `http://127.0.0.1:5173` (use IP instead of localhost if Spotify refuses http)
   - `https://jillpouchain.github.io/cassette-tape/`
4. Check **Web API** and **Web Playback SDK**
5. **Create a `.env` file** at the project root:
   ```env
   VITE_SPOTIFY_CLIENT_ID=your_client_id_here
   ```

📚 **Guides:**
- ✅ [SPOTIFY_PKCE_SETUP.md](./SPOTIFY_PKCE_SETUP.md) - **START HERE** - Simple Spotify setup (2024)
- 🔒 [SECURE_SETUP.md](./SECURE_SETUP.md) - Complete security guide
- 📝 [CREATE_ENV_FILE.md](./CREATE_ENV_FILE.md) - How to create .env file
- 🔧 [ENV_VARIABLES.md](./ENV_VARIABLES.md) - All environment variables explained
- 🔧 [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) - Fix common issues
- 🐛 [DEBUG_CHECKLIST.md](./DEBUG_CHECKLIST.md) - Step-by-step debugging

### 3. Development
```bash
npm run dev
```

### 4. First Use

1. Open http://localhost:5173
2. Click on the cassette
3. Authenticate with your Spotify account
4. Enjoy your music! 🎵

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
- **Watch the magic**: The tape reels animate in perfect sync with the song playing
- **Track info**: The current song and artist automatically display on the label
- **Mobile**: View in portrait mode - the cassette automatically rotates 90° for the best experience

### Change the default track

Edit `src/App.vue` line 17:
```typescript
const defaultTrackUri = ref('spotify:track:YOUR_TRACK_ID')
```

**How to find a Spotify track URI:**
1. Open Spotify (web or app)
2. Right-click on any song → Share → Copy song link
3. Extract the ID from the URL: `https://open.spotify.com/track/3n3Ppam7vgaVa1iaRUc9Lp`
4. Format as: `spotify:track:3n3Ppam7vgaVa1iaRUc9Lp`

## Tech Stack

- Vue 3 with Composition API
- TypeScript
- Vite
- Spotify Web Playback SDK
- Spotify Web API
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
