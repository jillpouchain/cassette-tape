<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

// Types
interface MusicTrack {
  id: string
  nom: string
  duree: number
  fichier: string
}

// État de la cassette
const isPlaying = ref(false)
const leftTapeSize = ref(85)
const rightTapeSize = ref(15)
const mixtapeTitle = ref('MIXTAPE 2025')

// Musique
const audioElement = ref<HTMLAudioElement | null>(null)
const currentTrack = ref<MusicTrack | null>(null)
const tracks = ref<MusicTrack[]>([])

// Chargement de la configuration des musiques
const loadMusicConfig = async () => {
  try {
    const response = await fetch('/musique/config.json')
    const data = await response.json()
    tracks.value = data
    
    if (tracks.value.length > 0) {
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
  
  const audio = new Audio(`/musique/${currentTrack.value.fichier}`)
  audio.volume = 0.7
  
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

onMounted(async () => {
  await loadMusicConfig()
  initAudio()
})

onUnmounted(() => {
  if (audioElement.value) {
    audioElement.value.pause()
    audioElement.value = null
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
