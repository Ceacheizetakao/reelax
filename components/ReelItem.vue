<template>
    <div
      class="reel"
      :style="{ transform: `translateY(${(index - currentIndex) * 100}%)` }"
    >
      <!-- VIDEO -->
      <template v-if="video.tipo === 'video'">
        <video
          ref="videoEl"
          :src="video.video_url"
          playsinline
          autoplay
          loop
          :muted="muted"
          @click="toggleMute"
          @timeupdate="updateProgress"
        ></video>
  
        <div class="progress-bar">
          <div class="progress-fill" :style="{ width: progress + '%' }"></div>
        </div>
  
        <div class="overlay">
          <p>{{ video.descripcion }}</p>
          <span>@{{ video.usuario }}</span>
          <span class="mute-icon">{{ muted ? '🔇' : '🔊' }}</span>
        </div>
      </template>
  
      <!-- FOTOS -->
      <template v-else-if="video.tipo === 'fotos'">
        <div class="carousel" ref="carouselRef" @scroll="handleScroll">
          <img
            v-for="(img, i) in video.imagenes"
            :key="i"
            :src="img"
            class="foto"
            :alt="video.descripcion"
          />
        </div>
  
        <!-- Indicadores tipo puntitos -->
        <div class="dots">
          <span
            v-for="(img, i) in video.imagenes"
            :key="i"
            :class="{ active: i === currentPhotoIndex }"
          >•</span>
        </div>
  
        <div class="overlay">
          <p>{{ video.descripcion }}</p>
          <span>@{{ video.usuario }}</span>
        </div>
      </template>
  
      <!-- Botones -->
      <div class="actions">
        <button class="action-button" @click.stop="$emit('toggle-like')">⭐</button>
        <button class="action-button" @click.stop="$emit('call')">📞</button>
        <button class="action-button" @click.stop="$emit('whatsapp')">💬</button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, watch, onMounted } from 'vue'
  
  const props = defineProps({
    video: Object,
    index: Number,
    currentIndex: Number,
    muted: Boolean,
    progress: Number
  })
  const emit = defineEmits(['toggle-mute', 'progress-update', 'toggle-like', 'call', 'whatsapp'])
  
  const videoEl = ref(null)
  const carouselRef = ref(null)
  const currentPhotoIndex = ref(0)
  
  const toggleMute = () => {
    videoEl.value.muted = !videoEl.value.muted
    emit('toggle-mute')
  }
  
  const updateProgress = (e) => {
    if (e.target.duration) {
      const value = (e.target.currentTime / e.target.duration) * 100
      emit('progress-update', value)
    }
  }
  
  const handleScroll = () => {
    if (carouselRef.value) {
      const scrollLeft = carouselRef.value.scrollLeft
      const width = carouselRef.value.clientWidth
      const index = Math.round(scrollLeft / width)
      currentPhotoIndex.value = index
    }
  }
  
  watch(() => props.currentIndex, (newVal) => {
    if (props.index === newVal && props.video.tipo === 'video') {
      videoEl.value?.play()
    } else {
      videoEl.value?.pause()
    }
  })
  
  onMounted(() => {
    if (props.index === props.currentIndex && props.video.tipo === 'video') {
      videoEl.value?.play()
    }
  })
  </script>
  
  <style scoped>
  .reel {
    height: 100dvh;
    width: 100%;
    position: absolute;
    transition: transform 0.4s ease;
    top: 0;
    left: 0;
    overflow: hidden;
  }
  
  video {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  
  .carousel {
    display: flex;
    height: 100%;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    scroll-behavior: smooth;
  }
  
  .foto {
    width: 100vw;
    height: 100%;
    object-fit: contain;
    padding-top: 5vh;
    padding-bottom: 5vh;
    scroll-snap-align: center;
    flex-shrink: 0;
    background-color: black;
  }
  
  .dots {
    position: absolute;
    bottom: 60px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 6px;
    font-size: 20px;
    z-index: 20;
    color: white;
    background-color: rgba(0, 0, 0, 0.4);
    padding: 4px 10px;
    border-radius: 20px;
  }
  
  .dots span {
    opacity: 0.5;
    transition: opacity 0.2s ease;
  }
  
  .dots span.active {
    opacity: 1;
  }
  
  .overlay {
    position: absolute;
    bottom: 100px;
    left: 20px;
    color: white;
    text-shadow: 0 0 5px black;
    font-size: 16px;
    z-index: 2;
  }
  
  .mute-icon {
    display: block;
    margin-top: 8px;
    font-size: 18px;
  }
  
  .progress-bar {
    position: absolute;
    bottom: 0;
    left: 0;
    height: 5px;
    width: 100%;
    background-color: rgba(255, 255, 255, 0.25);
    z-index: 3;
  }
  
  .progress-fill {
    height: 100%;
    background: linear-gradient(90deg, #d38f2a, #e1bd46);
    transition: width 0.1s linear;
  }
  
  .actions {
    position: absolute;
    bottom: 60px;
    right: 20px;
    display: flex;
    flex-direction: column;
    gap: 12px;
    z-index: 3;
  }
  
  .action-button {
    background: rgba(0, 0, 0, 0.4);
    border: none;
    color: white;
    font-size: 24px;
    border-radius: 50%;
    padding: 10px;
    transition: transform 0.2s ease;
    cursor: pointer;
  }
  
  .action-button:hover {
    transform: scale(1.1);
  }
  </style>
  