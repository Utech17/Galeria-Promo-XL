<template>
  <div class="min-h-screen bg-gray-50 text-gray-800 font-sans">
    <header class="bg-white shadow-sm sticky top-0 z-40">
      <div class="max-w-7xl mx-auto px-4 py-4 flex flex-col items-center gap-4">
        <h1 class="text-2xl font-bold tracking-tight text-blue-600 min-h-[2rem]">Promo XL - 2025 🎓 IUJO</h1>
        
        <nav class="flex p-1 space-x-1 bg-gray-100/80 rounded-xl min-h-[44px]">
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

        <div v-if="currentTab === 'gallery'" class="w-full max-w-7xl overflow-x-auto pb-2 min-h-[50px]">
           <div class="flex justify-center gap-2 min-w-max px-2">
              <button 
                v-for="cat in uniqueCategories" 
                :key="cat"
                @click="selectCategory(cat)"
                :class="['px-4 py-1.5 rounded-full text-sm border transition-colors',
                  selectedCategory === cat 
                    ? 'bg-blue-600 text-white border-blue-600' 
                    : 'bg-white text-gray-600 border-gray-200 hover:bg-gray-50']"
              >
                {{ cat }}
              </button>
           </div>
        </div>
      </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 py-8">
      <PinGrid 
        v-if="currentTab === 'gallery'"
        :images="displayedImages" 
        @open="showImg" 
        @download="handleDownload" 
      />

      <ActoReplay v-else />
    </main>

    <ClientOnly>
      <VueEasyLightbox
        :visible="visibleRef"
        :imgs="allFilteredImages.map(i => i.originalSrc)" 
        :index="indexRef"
        @hide="onHide"
      />
    </ClientOnly>

    <SpeedInsights />
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import { useInfiniteScroll } from '@vueuse/core'
import PinGrid from '../components/PinGrid.vue'
import ActoReplay from '../components/ActoReplay.vue'
import VueEasyLightbox from 'vue-easy-lightbox'
import { rawPhotos } from '../const/ID_fotos'
import { SpeedInsights } from "@vercel/speed-insights/nuxt"


// Función auxiliar para convertir ID de Drive a URL
const getDriveThumbnail = (id) => `https://lh3.googleusercontent.com/d/${id}=s500?authuser=0`
const getDriveFull = (id) => `https://lh3.googleusercontent.com/d/${id}=s1600?authuser=0`

const allImages = rawPhotos.map((item, index) => ({
  id: index,
  driveId: item.id,
  src: getDriveThumbnail(item.id),     // Versión optimizada para el grid
  originalSrc: getDriveFull(item.id),  // Versión alta calidad para lightbox
  category: item.cat,
  title: `Foto ${item.cat} ${index + 1}`
}))

const selectedCategory = ref('Todas')

const uniqueCategories = computed(() => {
  const cats = new Set(allImages.map(img => img.category))
  return ['Todas', ...cats]
})

const allFilteredImages = computed(() => {
  if (selectedCategory.value === 'Todas') {
    return allImages
  }
  return allImages.filter(img => img.category === selectedCategory.value)
})

const selectCategory = (cat) => {
  selectedCategory.value = cat
  displayedImages.value = []
  loadMore()
}

const currentTab = ref('gallery')

const displayedImages = ref([])
const pageSize = 25

const loadMore = () => {
  const currentLength = displayedImages.value.length
  const sourceList = allFilteredImages.value 
  
  if (currentLength >= sourceList.length) return

  const nextBatch = sourceList.slice(currentLength, currentLength + pageSize)
  displayedImages.value.push(...nextBatch)
}

watch(allFilteredImages, () => {
  displayedImages.value = []
  loadMore()
})

onMounted(() => {
  loadMore()
  useInfiniteScroll(window, () => {
    loadMore()
  }, { distance: 500 })
})

const visibleRef = ref(false)
const indexRef = ref(0)

const showImg = (indexInDisplayed) => {
  const imgClicked = displayedImages.value[indexInDisplayed]
  const realIndex = allFilteredImages.value.findIndex(img => img.id === imgClicked.id)
  
  indexRef.value = realIndex
  visibleRef.value = true
}

const onHide = () => visibleRef.value = false

const handleDownload = (img) => {
  const downloadLink = `https://drive.google.com/uc?export=download&id=${img.driveId}`
  window.open(downloadLink, '_blank')
}
</script>

<style>
html, body {
  background-color: #f9fafb; 
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