<template>
  <div class="min-h-screen bg-gray-50 text-gray-800 font-sans">
    
    <header class="bg-white shadow-sm sticky top-0 z-40">
      <div class="max-w-7xl mx-auto px-4 py-4 flex flex-col items-center gap-4">
        <h1 class="text-2xl font-bold tracking-tight text-blue-600">Promo XL - 2025 🎓 IUJO</h1>
        
        <nav class="flex p-1 space-x-1 bg-gray-100/80 rounded-xl">
          <button 
            @click="currentTab = 'gallery'"
            :class="['px-6 py-2.5 text-sm font-medium rounded-lg transition-all duration-200', 
              currentTab === 'gallery' ? 'bg-white text-blue-600 shadow-sm' : 'text-gray-500 hover:text-gray-700']"
          >
            Galería de Fotos
          </button>
          <button 
            @click="currentTab = 'live'"
            :class="['px-6 py-2.5 text-sm font-medium rounded-lg transition-all duration-200', 
              currentTab === 'live' ? 'bg-white text-blue-600 shadow-sm' : 'text-gray-500 hover:text-gray-700']"
          >
            Repetición Acto
          </button>
        </nav>
      </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 py-8">
      <PinGrid 
        v-if="currentTab === 'gallery'"
        :images="images" 
        @open="showImg" 
        @download="handleDownload" 
      />

      <ActoReplay v-else />
    </main>

    <ClientOnly>
      <VueEasyLightbox
        :visible="visibleRef"
        :imgs="images.map(i => i.src)"
        :index="indexRef"
        @hide="onHide"
      />
    </ClientOnly>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import PinGrid from '../components/PinGrid.vue'
import ActoReplay from '../components/ActoReplay.vue'
import VueEasyLightbox from 'vue-easy-lightbox'

// State
const currentTab = ref('gallery')

const imageFiles = import.meta.glob('../public/imagenes/*.{jpg,jpeg,png,webp,gif}', {
  eager: true,
  query: '?url',
})

const images = ref(
  Object.keys(imageFiles).map((path, index) => {
    const filename = path.split('/').pop()
    const title = filename.split('.')[0]
      .split(/[-_]/)
      .map(word => word.charAt(0).toUpperCase() + word.slice(1))
      .join(' ')

    return {
      id: index, // Simple index ID
      src: `/imagenes/${filename}`,
      title: title
    }
  })
)

const visibleRef = ref(false)
const indexRef = ref(0)

const showImg = (index) => {
  indexRef.value = index
  visibleRef.value = true
}

const onHide = () => visibleRef.value = false

// Download Handler
const handleDownload = async (img) => {
  try {
    const response = await fetch(img.src);
    const blob = await response.blob();
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = `Foto Promo XL - 2025 - ${img.title || 'download'}`;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  } catch (error) {
    console.error('Error al descargar:', error);
    window.open(img.src, '_blank');
  }
}
</script>

<style>
/* Personalizar scrollbar */
html, body {
  background-color: #f9fafb; /* bg-gray-50 matches */
  min-height: 100vh;
}
::-webkit-scrollbar {
  width: 8px;
}
::-webkit-scrollbar-track {
  background: #f1f1f1; 
}
::-webkit-scrollbar-thumb {
  background: #c7c7c7; 
  border-radius: 4px;
}
::-webkit-scrollbar-thumb:hover {
  background: #a0a0a0; 
}
</style>