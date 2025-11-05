<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const isPlaying = ref(false)
const leftTapeSize = ref(85)
const rightTapeSize = ref(15)
const mixtapeTitle = ref('MIXTAPE 2025')
const spotifyToken = ref('')
const spotifyPlayer = ref<any>(null)
const deviceId = ref('')
const currentTrack = ref<any>(null)
const trackDuration = ref(0)
const trackPosition = ref(0)
const isSpotifyReady = ref(false)

// Track Spotify par défaut (configurable via .env)
const defaultTrackUri = ref(
  import.meta.env.VITE_SPOTIFY_DEFAULT_TRACK || 'spotify:track:3n3Ppam7vgaVa1iaRUc9Lp'
)

// Détection automatique de l'URI de redirection
const getRedirectUri = () => {
  if (window.location.hostname === 'localhost' || window.location.hostname === '127.0.0.1') {
    return 'http://127.0.0.1:5173'
  }
  // URL de production depuis .env (obligatoire pour la production)
  const productionUrl = import.meta.env.VITE_PRODUCTION_URL
  if (!productionUrl) {
    console.error('❌ VITE_PRODUCTION_URL non définie dans .env')
    return window.location.origin + window.location.pathname
  }
  return productionUrl
}

// Générer un code verifier et challenge pour PKCE
const generateCodeVerifier = () => {
  const array = new Uint8Array(32)
  crypto.getRandomValues(array)
  return Array.from(array, byte => byte.toString(16).padStart(2, '0')).join('')
}

const generateCodeChallenge = async (verifier: string) => {
  const encoder = new TextEncoder()
  const data = encoder.encode(verifier)
  const hash = await crypto.subtle.digest('SHA-256', data)
  return btoa(String.fromCharCode(...new Uint8Array(hash)))
    .replace(/=/g, '')
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
}

// Fonction pour obtenir le token Spotify (OAuth with PKCE)
const getSpotifyToken = async () => {
  const clientId = import.meta.env.VITE_SPOTIFY_CLIENT_ID
  
  if (!clientId) {
    console.error('❌ VITE_SPOTIFY_CLIENT_ID non défini dans les variables d\'environnement')
    alert('Configuration Spotify manquante. Consultez le fichier SPOTIFY_SETUP.md')
    return
  }
  
  const redirectUri = getRedirectUri()
  const scopes = 'streaming user-read-email user-read-private user-modify-playback-state'
  
  // Générer PKCE codes
  const codeVerifier = generateCodeVerifier()
  const codeChallenge = await generateCodeChallenge(codeVerifier)
  
  // Stocker le verifier pour l'utiliser après le callback
  localStorage.setItem('code_verifier', codeVerifier)
  
  const authUrl = `https://accounts.spotify.com/authorize?client_id=${clientId}&response_type=code&redirect_uri=${encodeURIComponent(redirectUri)}&scope=${encodeURIComponent(scopes)}&code_challenge_method=S256&code_challenge=${codeChallenge}`
  
  window.location.href = authUrl
}

// Échanger le code contre un token
const exchangeCodeForToken = async (code: string) => {
  const clientId = import.meta.env.VITE_SPOTIFY_CLIENT_ID
  const redirectUri = getRedirectUri()
  const codeVerifier = localStorage.getItem('code_verifier')
  
  if (!codeVerifier) {
    console.error('❌ Code verifier manquant')
    return
  }
  
  const body = new URLSearchParams({
    grant_type: 'authorization_code',
    code: code,
    redirect_uri: redirectUri,
    client_id: clientId,
    code_verifier: codeVerifier
  })
  
  try {
    const response = await fetch('https://accounts.spotify.com/api/token', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded'
      },
      body: body.toString()
    })
    
    const data = await response.json()
    
    if (data.access_token) {
      spotifyToken.value = data.access_token
      
      // Sauvegarder le token et son heure d'expiration (1h par défaut)
      const expiresIn = data.expires_in || 3600 // 3600 secondes = 1h
      const expiresAt = Date.now() + (expiresIn * 1000)
      localStorage.setItem('spotify_token', data.access_token)
      localStorage.setItem('spotify_token_expires_at', expiresAt.toString())
      
      localStorage.removeItem('code_verifier')
      // Nettoyer l'URL
      window.history.replaceState({}, document.title, window.location.pathname)
      // Initialiser le player
      initSpotifyPlayer()
    } else {
      console.error('❌ Erreur lors de l\'échange du code:', data)
    }
  } catch (error) {
    console.error('❌ Erreur lors de l\'échange du code:', error)
  }
}

// Initialiser le player Spotify
const initSpotifyPlayer = () => {
  const script = document.createElement('script')
  script.src = 'https://sdk.scdn.co/spotify-player.js'
  script.async = true
  document.body.appendChild(script)

  ;(window as any).onSpotifyWebPlaybackSDKReady = () => {
    const player = new (window as any).Spotify.Player({
      name: 'Cassette Tape Player',
      getOAuthToken: (cb: any) => { cb(spotifyToken.value) },
      volume: 0.7
    })

    // Player prêt
    player.addListener('ready', ({ device_id }: any) => {
      console.log('✅ Spotify Player Ready - Device ID:', device_id)
      deviceId.value = device_id
      isSpotifyReady.value = true
      
      // Jouer automatiquement la track par défaut
      if (defaultTrackUri.value) {
        playTrack(defaultTrackUri.value)
      }
    })

    // État du player changé
    player.addListener('player_state_changed', (state: any) => {
      if (!state) return
      
      currentTrack.value = state.track_window.current_track
      trackDuration.value = state.duration
      trackPosition.value = state.position
      isPlaying.value = !state.paused
      
      // Synchroniser les bobines avec la vraie progression
      if (trackDuration.value > 0) {
        const progress = (trackPosition.value / trackDuration.value) * 100
        leftTapeSize.value = 85 - (progress * 0.70) // Va de 85% à 15%
        rightTapeSize.value = 15 + (progress * 0.70) // Va de 15% à 85%
      }
      
      // Afficher le titre de la track sur l'étiquette
      if (currentTrack.value) {
        mixtapeTitle.value = `${currentTrack.value.name} - ${currentTrack.value.artists[0].name}`.toUpperCase()
      }
      
      // La track est terminée
      if (state.position === 0 && state.paused) {
        leftTapeSize.value = 85
        rightTapeSize.value = 15
      }
    })

    player.addListener('initialization_error', ({ message }: any) => {
      console.error('❌ Initialization Error:', message)
    })

    player.addListener('authentication_error', ({ message }: any) => {
      console.error('❌ Authentication Error:', message)
      alert('Erreur d\'authentification Spotify. Reconnectez-vous.')
    })

    player.addListener('account_error', ({ message }: any) => {
      console.error('❌ Account Error:', message)
      alert('Spotify Premium requis pour utiliser ce player.')
    })

    player.connect()
    spotifyPlayer.value = player
  }
}

// Jouer une track spécifique
const playTrack = async (trackUri: string) => {
  if (!deviceId.value || !spotifyToken.value) return
  
  try {
    const response = await fetch(`https://api.spotify.com/v1/me/player/play?device_id=${deviceId.value}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${spotifyToken.value}`
      },
      body: JSON.stringify({
        uris: [trackUri]
      })
    })
    
    if (!response.ok) {
      console.error('Erreur lors de la lecture:', await response.text())
    }
  } catch (error) {
    console.error('Erreur lors de la lecture:', error)
  }
}

// Toggle Play/Pause
const togglePlay = () => {
  if (!spotifyPlayer.value) {
    // Si pas encore authentifié, rediriger vers Spotify
    getSpotifyToken()
    return
  }
  
  spotifyPlayer.value.togglePlay()
}

onMounted(() => {
  // Vérifier s'il y a un token valide dans localStorage
  const savedToken = localStorage.getItem('spotify_token')
  const expiresAt = localStorage.getItem('spotify_token_expires_at')
  
  if (savedToken && expiresAt) {
    const now = Date.now()
    const tokenExpiresAt = parseInt(expiresAt)
    
    // Si le token n'a pas expiré, l'utiliser directement
    if (now < tokenExpiresAt) {
      console.log('✅ Token Spotify valide trouvé dans localStorage')
      spotifyToken.value = savedToken
      initSpotifyPlayer()
      return
    } else {
      // Token expiré, le supprimer
      console.log('⏰ Token Spotify expiré, nouvelle authentification nécessaire')
      localStorage.removeItem('spotify_token')
      localStorage.removeItem('spotify_token_expires_at')
    }
  }
  
  // Récupérer le code de l'URL après auth (PKCE)
  const urlParams = new URLSearchParams(window.location.search)
  const code = urlParams.get('code')
  
  if (code) {
    // Échanger le code contre un token
    exchangeCodeForToken(code)
  }
})

onUnmounted(() => {
  if (spotifyPlayer.value) {
    spotifyPlayer.value.disconnect()
  }
})
</script>

<template>
  <div class="container">
    <div class="cassette" @click="togglePlay">
      <!-- Corps principal de la cassette -->
      <div class="cassette-body">
        <!-- Cadre extérieur -->
        <div class="outer-frame"></div>
        
        <!-- Bande de titre éditable -->
        <div class="title-band">
          <input 
            v-model="mixtapeTitle" 
            class="title-input"
            @click.stop
            maxlength="30"
          />
        </div>
        
        <!-- Zone centrale avec les bobines -->
        <div class="tape-window">
          <!-- Deux bobines avec bandes magnétiques -->
          <div class="reel-container">
            <!-- Bobine gauche -->
            <div class="reel left" :style="{ '--size': leftTapeSize }">
              <div class="reel-center">
                <div class="reel-inner">
                  <div class="reel-hole" :class="{ spinning: isPlaying }">
                    <div class="hole-teeth"></div>
                  </div>
                </div>
              </div>
            </div>
            
            <!-- Bobine droite -->
            <div class="reel right" :style="{ '--size': rightTapeSize }">
              <div class="reel-center">
                <div class="reel-inner">
                  <div class="reel-hole" :class="{ spinning: isPlaying }">
                    <div class="hole-teeth"></div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        
        <!-- Vis de coins -->
        <div class="screw top-left"></div>
        <div class="screw top-right"></div>
        <div class="screw bottom-left"></div>
        <div class="screw bottom-right"></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(180deg, #1a1a1a 0%, #0a0a0a 100%);
  padding: 20px;
}

.cassette {
  cursor: pointer;
  transition: transform 0.2s;
}

.cassette:hover {
  transform: scale(1.02);
}

.cassette:active {
  transform: scale(0.99);
}

.cassette-body {
  position: relative;
  width: 500px;
  height: 320px;
  background: linear-gradient(145deg, #3a3a3a 0%, #2a2a2a 100%);
  border-radius: 12px;
  padding: 20px;
  box-shadow: 
    0 8px 24px rgba(0, 0, 0, 0.6),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
  transition: transform 0.2s;
}

/* Mobile - always rotate cassette 90° (portrait or landscape) */
@media (max-width: 768px) {
  .container {
    padding: 0;
    overflow: hidden;
  }
  
  .cassette-body {
    /* Largeur = hauteur viewport (car rotation 90°) */
    /* Ratio original: 500/320 = 1.5625 */
    width: calc(100vh - 40px);
    height: calc((100vh - 40px) / 1.5625);
    max-width: 600px;
    max-height: 384px;
    padding: 3.2%;
    transform: rotate(90deg);
    overflow: hidden;
  }
  
  .cassette:hover .cassette-body {
    transform: rotate(90deg) scale(1.02);
  }
  
  .cassette:active .cassette-body {
    transform: rotate(90deg) scale(0.99);
  }
  
  .title-band {
    height: 8%;
    margin-bottom: 2%;
  }
  
  .title-input {
    font-size: clamp(12px, 3vw, 16px);
  }
  
  .tape-window {
    height: 75%;
    padding: 4% 6%;
    margin: 0;
  }
  
  .reel-container {
    gap: 8%;
  }
  
  .reel {
    max-width: 30%;
  }
  
  .outer-frame {
    border-width: 2px;
  }
  
  .screw {
    width: 2.5%;
    height: 2.5%;
    min-width: 10px;
    min-height: 10px;
  }
  
  .screw::before {
    height: 1.5px;
  }
  
  .screw.top-left,
  .screw.top-right {
    top: 4%;
  }
  
  .screw.top-left,
  .screw.bottom-left {
    left: 4%;
  }
  
  .screw.top-right,
  .screw.bottom-right {
    right: 4%;
  }
  
  .screw.bottom-left,
  .screw.bottom-right {
    bottom: 4%;
  }
}

.outer-frame {
  position: absolute;
  top: 8px;
  left: 8px;
  right: 8px;
  bottom: 8px;
  border: 2px solid rgba(200, 200, 200, 0.4);
  border-radius: 10px;
  pointer-events: none;
}

.title-band {
  position: relative;
  height: 40px;
  background: linear-gradient(180deg, #e8e8e8 0%, #d0d0d0 100%);
  border-radius: 4px;
  margin-bottom: 15px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 
    inset 0 2px 4px rgba(0, 0, 0, 0.15),
    0 1px 2px rgba(0, 0, 0, 0.2);
}

.title-input {
  background: transparent;
  border: none;
  outline: none;
  text-align: center;
  font-size: 16px;
  font-weight: 600;
  color: #1a1a1a;
  font-family: 'Courier New', monospace;
  letter-spacing: 2px;
  text-transform: uppercase;
  width: 90%;
  cursor: text;
}

.title-input::selection {
  background: rgba(200, 50, 50, 0.3);
}

.tape-window {
  position: relative;
  height: 200px;
  background: linear-gradient(145deg, #0a0a0a 0%, #1a1a1a 100%);
  border-radius: 8px;
  padding: 30px 40px;
  box-shadow: 
    inset 0 4px 12px rgba(0, 0, 0, 0.8),
    inset 0 0 20px rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(50, 50, 50, 0.8);
}

.reel-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100%;
  position: relative;
  gap: 60px;
}

.reel {
  position: relative;
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  max-width: 140px;
}

.reel-center {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: all 0.05s linear;
}

.reel.left .reel-center {
  width: calc(100px + var(--size) * 0.6px);
  height: calc(100px + var(--size) * 0.6px);
  background: radial-gradient(circle, 
    #d32f2f 0%,
    #c62828 30%,
    #b71c1c 60%,
    #8b0000 100%);
  box-shadow: 
    0 2px 8px rgba(0, 0, 0, 0.4),
    inset 0 -2px 8px rgba(0, 0, 0, 0.3),
    inset 0 2px 4px rgba(255, 100, 100, 0.3);
}

.reel.right .reel-center {
  width: calc(100px + var(--size) * 0.6px);
  height: calc(100px + var(--size) * 0.6px);
  background: radial-gradient(circle, 
    #d32f2f 0%,
    #c62828 30%,
    #b71c1c 60%,
    #8b0000 100%);
  box-shadow: 
    0 2px 8px rgba(0, 0, 0, 0.4),
    inset 0 -2px 8px rgba(0, 0, 0, 0.3),
    inset 0 2px 4px rgba(255, 100, 100, 0.3);
}

.reel-inner {
  width: 70%;
  height: 70%;
  border-radius: 50%;
  background: radial-gradient(circle,
    #a31f1f 0%,
    #8b1818 50%,
    #6d0000 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 
    inset 0 2px 6px rgba(0, 0, 0, 0.5),
    0 1px 3px rgba(255, 100, 100, 0.2);
}

.reel-hole {
  width: 45%;
  height: 45%;
  background: #0a0a0a;
  border-radius: 50%;
  box-shadow: 
    inset 0 2px 8px rgba(0, 0, 0, 1),
    0 0 0 2px rgba(50, 50, 50, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.hole-teeth {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background: repeating-conic-gradient(
    from 0deg,
    #1a1a1a 0deg 18deg,
    #0a0a0a 18deg 36deg
  );
}

.reel-hole.spinning {
  animation: spin 2s linear infinite;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.screw {
  position: absolute;
  width: 14px;
  height: 14px;
  background: radial-gradient(circle, #888 0%, #444 70%, #222 100%);
  border-radius: 50%;
  box-shadow: 
    inset 0 1px 2px rgba(255, 255, 255, 0.4),
    inset 0 -1px 2px rgba(0, 0, 0, 0.4),
    0 2px 4px rgba(0, 0, 0, 0.3);
}

.screw::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 70%;
  height: 2px;
  background: #1a1a1a;
  box-shadow: 0 0 1px rgba(0, 0, 0, 0.5);
}

.screw.top-left {
  top: 25px;
  left: 25px;
}

.screw.top-right {
  top: 25px;
  right: 25px;
}

.screw.bottom-left {
  bottom: 25px;
  left: 25px;
}

.screw.bottom-right {
  bottom: 25px;
  right: 25px;
}
</style>
