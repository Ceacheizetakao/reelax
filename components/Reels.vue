<template>
    <div
      class="reels-container"
      @touchstart="onTouchStart"
      @touchend="onTouchEnd"
      @mousedown="onMouseDown"
      @mouseup="onMouseUp"
    >
      <ReelItem
        v-for="(video, i) in visibleVideos"
        :key="video.id"
        :index="i + currentIndex - 1"
        :currentIndex="currentIndex"
        :video="video"
        :muted="muted[i + currentIndex - 1]"
        :progress="progress[i + currentIndex - 1]"
        @toggle-mute="toggleMute(i + currentIndex - 1)"
        @progress-update="updateProgress(i + currentIndex - 1, $event)"
        @toggle-like="toggleLike(i + currentIndex - 1)"
        @call="callUser(video.usuario)"
        @whatsapp="whatsappUser(video.usuario)"
      />
    </div>
  </template>
  
  <script setup>
  import { ref, computed } from 'vue'
  import ReelItem from './ReelItem.vue'
  
  // Lista completa de reels (videos + uno de fotos)
  const originalVideos = [
    {
      id: 1,
      tipo: 'video',
      video_url: '/videos/v1.mp4',
      descripcion: 'Reel video 1',
      usuario: 'usuario1'
    },
    {
      id: 2,
      tipo: 'video',
      video_url: '/videos/v2.mp4',
      descripcion: 'Reel video 2',
      usuario: 'usuario2'
    },
    {
      id: 3,
      tipo: 'video',
      video_url: '/videos/v3.mp4',
      descripcion: 'Reel video 3',
      usuario: 'usuario3'
    },
    {
      id: 4,
      tipo: 'video',
      video_url: '/videos/v4.mp4',
      descripcion: 'Reel video 4',
      usuario: 'usuario4'
    },
    {
      id: 5,
      tipo: 'video',
      video_url: '/videos/v5.mp4',
      descripcion: 'Reel video 5',
      usuario: 'usuario5'
    },
    {
      id: 6,
      tipo: 'video',
      video_url: '/videos/v6.mp4',
      descripcion: 'Reel video 6',
      usuario: 'usuario6'
    },
    {
      id: 7,
      tipo: 'fotos',
      imagenes: [
        '/videos/fotos/foto1.jpg',
        '/videos/fotos/foto2.jpg',
        '/videos/fotos/foto3.jpg',
        '/videos/fotos/foto4.jpg',
        '/videos/fotos/foto5.jpg'
      ],
      descripcion: 'Reel de fotos',
      usuario: 'usuario_fotos'
    }
  ]
  
  // Mezclar orden de los reels
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
  const liked = ref(videos.value.map(() => false))
  
  const visibleVideos = computed(() =>
    videos.value.slice(
      Math.max(0, currentIndex.value - 1),
      Math.min(videos.value.length, currentIndex.value + 2)
    )
  )
  
  let touchStartY = 0
  let mouseStartY = 0
  
  const onTouchStart = (e) => {
    touchStartY = e.touches[0].clientY
  }
  const onTouchEnd = (e) => {
    const deltaY = e.changedTouches[0].clientY - touchStartY
    handleSwipe(deltaY)
  }
  const onMouseDown = (e) => {
    mouseStartY = e.clientY
  }
  const onMouseUp = (e) => {
    const deltaY = e.clientY - mouseStartY
    handleSwipe(deltaY)
  }
  const handleSwipe = (deltaY) => {
    const threshold = 50
    if (deltaY < -threshold && currentIndex.value < videos.value.length - 1) {
      currentIndex.value++
    } else if (deltaY > threshold && currentIndex.value > 0) {
      currentIndex.value--
    }
  }
  
  const toggleMute = (index) => () => {
    muted.value[index] = !muted.value[index]
  }
  const updateProgress = (index, value) => {
    progress.value[index] = value
  }
  const toggleLike = (index) => {
    liked.value[index] = !liked.value[index]
  }
  const callUser = (usuario) => {
    alert(`Llamando a ${usuario}...`)
  }
  const whatsappUser = (usuario) => {
    alert(`Abriendo WhatsApp con ${usuario}...`)
  }
  </script>
  
  <style scoped>
  .reels-container {
    height: 100vh;
    width: 100%;
    overflow: hidden;
    position: relative;
  }
  </style>
  