<script setup lang="ts">
import { ref, onMounted, onUnmounted, computed } from 'vue'

// Types
interface MusicTrack {
  id: number
  nom: string
  duree: number
  fichier: string
  color: string
}

// Récupérer l'ID depuis l'URL (supporte ?id=2 ou /2)
const getTrackIdFromUrl = (): number => {
  // D'abord, essayer les query parameters (?id=2)
  const params = new URLSearchParams(window.location.search)
  const queryId = params.get('id')
  if (queryId) {
    return parseInt(queryId)
  }
  
  // Sinon, essayer de récupérer depuis le path (/2)
  const path = window.location.pathname
  const basePath = import.meta.env.BASE_URL || '/'
  const pathAfterBase = path.replace(basePath, '')
  const pathId = pathAfterBase.replace(/^\/+|\/+$/g, '') // Enlever les slashes au début/fin
  
  if (pathId && !isNaN(parseInt(pathId))) {
    return parseInt(pathId)
  }
  
  // Par défaut, retourner 1
  return 1
}

// État de la cassette
const isPlaying = ref(false)
const leftTapeSize = ref(85)
const rightTapeSize = ref(15)
const mixtapeTitle = ref('MIXTAPE 2025')

// Couleur de la cassette basée sur la piste
const tapeColor = computed(() => {
  if (!currentTrack.value || !currentTrack.value.color) return 'red'
  return currentTrack.value.color
})

// Musique
const audioElement = ref<HTMLAudioElement | null>(null)
const currentTrack = ref<MusicTrack | null>(null)
const tracks = ref<MusicTrack[]>([])

// Chargement de la configuration des musiques
const loadMusicConfig = async () => {
  try {
    // Utiliser import.meta.env.BASE_URL pour supporter GitHub Pages
    const basePath = import.meta.env.BASE_URL || '/'
    const configPath = `${basePath}musique/config.json`
    
    const response = await fetch(configPath)
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`)
    }
    
    const data = await response.json()
    tracks.value = data
    
    // Récupérer l'ID depuis les query parameters
    const trackId = getTrackIdFromUrl()
    
    // Chercher la piste correspondant à l'ID
    const selectedTrack = tracks.value.find(track => track.id === trackId)
    
    if (selectedTrack) {
      currentTrack.value = selectedTrack
      mixtapeTitle.value = selectedTrack.nom.toUpperCase()
    } else if (tracks.value.length > 0) {
      // Si l'ID n'existe pas, charger la première piste
      const firstTrack = tracks.value[0]
      if (firstTrack) {
        currentTrack.value = firstTrack
        mixtapeTitle.value = firstTrack.nom.toUpperCase()
      }
    }
  } catch (error) {
    console.error('❌ Erreur lors du chargement de la configuration:', error)
  }
}

// Initialiser l'audio
const initAudio = () => {
  if (!currentTrack.value) return
  
  // Utiliser import.meta.env.BASE_URL pour supporter GitHub Pages
  const basePath = import.meta.env.BASE_URL || '/'
  const audioPath = `${basePath}musique/${currentTrack.value.fichier}`
  
  const audio = new Audio(audioPath)
  audio.volume = 0.7
  audio.preload = 'auto'
  
  // Événement : musique terminée
  audio.addEventListener('ended', () => {
    isPlaying.value = false
    leftTapeSize.value = 85
    rightTapeSize.value = 15
  })
  
  // Événement : mise à jour de la progression
  audio.addEventListener('timeupdate', () => {
    if (currentTrack.value && currentTrack.value.duree > 0) {
      const progress = (audio.currentTime / currentTrack.value.duree) * 100
      leftTapeSize.value = 85 - (progress * 0.70) // Va de 85% à 15%
      rightTapeSize.value = 15 + (progress * 0.70) // Va de 15% à 85%
    }
  })
  
  audioElement.value = audio
}

// Toggle Play/Pause
const togglePlay = () => {
  if (!audioElement.value) {
    initAudio()
  }
  
  if (!audioElement.value) return
  
  if (isPlaying.value) {
    audioElement.value.pause()
    isPlaying.value = false
  } else {
    audioElement.value.play()
    isPlaying.value = true
  }
}

// Fonction pour recharger la piste quand l'URL change
const reloadTrack = async () => {
  // Arrêter la musique actuelle si elle joue
  if (audioElement.value) {
    audioElement.value.pause()
    audioElement.value = null
  }
  
  // Réinitialiser l'état
  isPlaying.value = false
  leftTapeSize.value = 85
  rightTapeSize.value = 15
  
  // Recharger la configuration et la piste
  await loadMusicConfig()
  initAudio()
  
  // Démarrer la lecture automatiquement
  setTimeout(() => {
    if (audioElement.value) {
      // Essayer d'abord avec le son
      audioElement.value.play().then(() => {
        isPlaying.value = true
      }).catch((error: unknown) => {
        // Si l'autoplay avec son est bloqué, essayer en mode muted
        console.log('Autoplay avec son bloqué, essai en mode muet:', error)
        if (audioElement.value) {
          const originalVolume = audioElement.value.volume
          audioElement.value.muted = true
          audioElement.value.play().then(() => {
            isPlaying.value = true
            // Réactiver le son après 500ms
            setTimeout(() => {
              if (audioElement.value) {
                audioElement.value.muted = false
                audioElement.value.volume = originalVolume
              }
            }, 500)
          }).catch((muteError: unknown) => {
            console.log('Autoplay complètement bloqué:', muteError)
          })
        }
      })
    }
  }, 100)
}

onMounted(async () => {
  await loadMusicConfig()
  initAudio()
  
  // Démarrer la lecture automatiquement (avec un petit délai pour s'assurer que l'audio est initialisé)
  setTimeout(() => {
    if (audioElement.value) {
      // Essayer d'abord avec le son
      audioElement.value.play().then(() => {
        isPlaying.value = true
      }).catch((error: unknown) => {
        // Si l'autoplay avec son est bloqué, essayer en mode muted
        console.log('Autoplay avec son bloqué, essai en mode muet:', error)
        if (audioElement.value) {
          const originalVolume = audioElement.value.volume
          audioElement.value.muted = true
          audioElement.value.play().then(() => {
            isPlaying.value = true
            // Réactiver le son après 500ms
            setTimeout(() => {
              if (audioElement.value) {
                audioElement.value.muted = false
                audioElement.value.volume = originalVolume
              }
            }, 500)
          }).catch((muteError: unknown) => {
            console.log('Autoplay complètement bloqué:', muteError)
            // L'utilisateur devra cliquer sur la cassette
          })
        }
      })
    }
  }, 100)
  
  // Écouter les changements d'URL (navigation avec les boutons précédent/suivant)
  window.addEventListener('popstate', reloadTrack)
})

onUnmounted(() => {
  if (audioElement.value) {
    audioElement.value.pause()
    audioElement.value = null
  }
  
  // Retirer l'écouteur d'événements
  window.removeEventListener('popstate', reloadTrack)
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
            <div class="reel left" :style="{ '--size': leftTapeSize, '--tape-color': tapeColor }">
              <div class="reel-center">
                <div class="reel-inner">
                  <div class="reel-hole" :class="{ spinning: isPlaying }">
                    <div class="hole-teeth"></div>
                  </div>
                </div>
              </div>
            </div>
            
            <!-- Bobine droite -->
            <div class="reel right" :style="{ '--size': rightTapeSize, '--tape-color': tapeColor }">
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

.reel.left .reel-center,
.reel.right .reel-center {
  width: calc(100px + var(--size) * 0.6px);
  height: calc(100px + var(--size) * 0.6px);
  background: radial-gradient(circle, 
    color-mix(in srgb, var(--tape-color, red) 100%, white 20%) 0%,
    color-mix(in srgb, var(--tape-color, red) 100%, white 10%) 30%,
    color-mix(in srgb, var(--tape-color, red) 100%, black 20%) 60%,
    color-mix(in srgb, var(--tape-color, red) 100%, black 40%) 100%);
  box-shadow: 
    0 2px 8px rgba(0, 0, 0, 0.4),
    inset 0 -2px 8px rgba(0, 0, 0, 0.3),
    inset 0 2px 4px color-mix(in srgb, var(--tape-color, red) 50%, white 50%);
}

.reel-inner {
  width: 70%;
  height: 70%;
  border-radius: 50%;
  background: radial-gradient(circle,
    color-mix(in srgb, var(--tape-color, red) 100%, black 30%) 0%,
    color-mix(in srgb, var(--tape-color, red) 100%, black 40%) 50%,
    color-mix(in srgb, var(--tape-color, red) 100%, black 55%) 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 
    inset 0 2px 6px rgba(0, 0, 0, 0.5),
    0 1px 3px color-mix(in srgb, var(--tape-color, red) 50%, white 50%);
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
