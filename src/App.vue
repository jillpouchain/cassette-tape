<script setup lang="ts">
import { ref, onUnmounted } from 'vue'

const isPlaying = ref(false)
const leftTapeSize = ref(85)
const rightTapeSize = ref(15)
const mixtapeTitle = ref('MIXTAPE 2025')
let animationInterval: number | null = null

const togglePlay = () => {
  isPlaying.value = !isPlaying.value
  
  if (isPlaying.value) {
    startAnimation()
  } else {
    stopAnimation()
  }
}

const startAnimation = () => {
  animationInterval = setInterval(() => {
    // La bande de gauche diminue, celle de droite augmente
    leftTapeSize.value -= 0.3
    rightTapeSize.value += 0.3
    
    // Quand on arrive au bout, on arrête
    if (leftTapeSize.value <= 10) {
      stopAnimation()
      isPlaying.value = false
    }
  }, 50)
}

const stopAnimation = () => {
  if (animationInterval !== null) {
    clearInterval(animationInterval)
    animationInterval = null
  }
}

onUnmounted(() => {
  stopAnimation()
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
      
      <!-- Indicateur de statut -->
      <div class="status">
        {{ isPlaying ? '▶ PLAYING' : '⏸ PAUSED' }}
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

/* Mobile - always rotate cassette 90° (portrait or landscape) */
@media (max-width: 768px) {
  .container {
    padding: 10px;
  }
  
  .cassette {
    transform: rotate(90deg) !important;
  }
  
  .cassette:hover {
    transform: rotate(90deg) scale(1.02) !important;
  }
  
  .cassette:active {
    transform: rotate(90deg) scale(0.99) !important;
  }
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
}

/* Responsive sizing for tablets */
@media (max-width: 768px) {
  .cassette-body {
    width: 400px;
    height: 256px;
    padding: 16px;
  }
  
  .status {
    font-size: 16px;
    margin-top: 20px;
  }
}

/* Responsive sizing for small mobile */
@media (max-width: 480px) {
  .cassette-body {
    width: 340px;
    height: 217px;
    padding: 14px;
  }
  
  .title-band {
    height: 32px;
    margin-bottom: 12px;
  }
  
  .title-input {
    font-size: 13px;
  }
  
  .tape-window {
    height: 150px;
    padding: 20px 30px;
  }
  
  .status {
    font-size: 14px;
    margin-top: 15px;
  }
  
  .screw {
    width: 10px;
    height: 10px;
  }
  
  .screw.top-left,
  .screw.top-right {
    top: 20px;
  }
  
  .screw.top-left,
  .screw.bottom-left {
    left: 20px;
  }
  
  .screw.top-right,
  .screw.bottom-right {
    right: 20px;
  }
  
  .screw.bottom-left,
  .screw.bottom-right {
    bottom: 20px;
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

.status {
  text-align: center;
  margin-top: 25px;
  font-size: 20px;
  font-weight: 600;
  color: #ccc;
  font-family: 'Arial', sans-serif;
  letter-spacing: 2px;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5);
}
</style>
