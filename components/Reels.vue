<template>
  <div class="reels-container"
       @touchstart="onTouchStart"
       @touchmove="onTouchMove"
       @touchend="onTouchEnd"
       @mousedown="onMouseDown"
       @mousemove="onMouseMove"
       @mouseup="onMouseUp"
       @mouseleave="onMouseUp">

    <div
      class="reels-wrapper"
      :style="{
        transform: `translateY(${translateY}px)`,
        transition: isAnimating ? 'transform 0.3s ease' : 'none'
      }"
    >
      <ReelItem
        v-for="(video, i) in videos"
        :key="video.id"
        :index="i"
        :currentIndex="currentIndex"
        :video="video"
        :muted="muted[i]"
        :progress="progress[i]"
        @toggle-mute="toggleMute(i)"
        @progress-update="updateProgress(i, $event)"
        @toggle-like="toggleLike(i)"
        @call="callUser(video.usuario)"
        @whatsapp="whatsappUser(video.usuario)"
      />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ReelItem from './ReelItem.vue'

const originalVideos = [
  { id: 1, tipo: 'video', video_url: '/videos/v1.mp4', descripcion: 'Reel 1', usuario: 'usuario1' },
  { id: 2, tipo: 'video', video_url: '/videos/v2.mp4', descripcion: 'Reel 2', usuario: 'usuario2' },
  { id: 3, tipo: 'fotos', imagenes: ['/videos/fotos/foto1.jpg', '/videos/fotos/foto2.jpg'], descripcion: 'Fotos', usuario: 'usuario_fotos' },
  { id: 4, tipo: 'video', video_url: '/videos/v3.mp4', descripcion: 'Reel 3', usuario: 'usuario3' }
]

function shuffleArray(array) {
  const shuffled = [...array]
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1))
    ;[shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]]
  }
  return shuffled
}

const videos = ref(shuffleArray(originalVideos))
const currentIndex = ref(0)
const muted = ref(videos.value.map(() => true))
const progress = ref(videos.value.map(() => 0))

// --- Drag State ---
const startY = ref(0)
const deltaY = ref(0)
const translateY = ref(0)
const isDragging = ref(false)
const isAnimating = ref(false)

const onTouchStart = (e) => startDrag(e.touches[0].clientY)
const onTouchMove = (e) => drag(e.touches[0].clientY)
const onTouchEnd = () => endDrag()

const onMouseDown = (e) => startDrag(e.clientY)
const onMouseMove = (e) => { if (isDragging.value) drag(e.clientY) }
const onMouseUp = () => { if (isDragging.value) endDrag() }

function startDrag(y) {
  startY.value = y
  deltaY.value = 0
  isDragging.value = true
  isAnimating.value = false
}

function drag(currentY) {
  deltaY.value = currentY - startY.value
  translateY.value = deltaY.value
}

function endDrag() {
  isDragging.value = false
  isAnimating.value = true
  const threshold = 100
  if (deltaY.value < -threshold && currentIndex.value < videos.value.length - 1) {
    currentIndex.value++
  } else if (deltaY.value > threshold && currentIndex.value > 0) {
    currentIndex.value--
  }
  translateY.value = 0
  setTimeout(() => (isAnimating.value = false), 300)
}

const toggleMute = (index) => () => {
  muted.value[index] = !muted.value[index]
}
const updateProgress = (index, value) => {
  progress.value[index] = value
}
const toggleLike = (index) => {
  console.log('Like', index)
}
const callUser = (usuario) => {
  alert(`Llamando a ${usuario}`)
}
const whatsappUser = (usuario) => {
  alert(`Abriendo WhatsApp con ${usuario}`)
}
</script>

<style scoped>
.reels-container {
  height: 100dvh;
  overflow: hidden;
  position: relative;
}
.reels-wrapper {
  height: 100%;
  width: 100%;
  position: relative;
}
</style>

<!--
💡 Este archivo ya está preparado para reemplazar el sistema de drag manual por GSAP Draggable.

Donde dice:
  translateY.value = ...
  transition: transform 0.3s ease

Puedes reemplazar por:
  gsap.to(..., { y: ..., ease: 'power3.out' })
  Draggable.create(...) para arrastre real con snapping, inertia, etc.
-->
