<template>
  <!-- Wrapper Grid: Defines the number of columns structurally -->
  <div class="mx-auto grid grid-cols-2 md:grid-cols-4 xl:grid-cols-6 gap-4 items-start">
    
    <!-- Render each column as a vertical stack -->
    <div 
      v-for="(col, colIndex) in columns" 
      :key="colIndex" 
      class="flex flex-col gap-4"
    >
      <div 
        v-for="img in col" 
        :key="img.id"
        class="relative group w-full"
        @click="$emit('open', images.indexOf(img))"
      >
        <div class="cursor-zoom-in rounded-2xl overflow-hidden relative border border-gray-200 bg-gray-100">
           <!-- Added minimal height/skeleton protection with bg-gray-100 -->
           <NuxtImg 
             :src="img.src" 
             class="w-full h-auto object-cover transform transition duration-300 hover:brightness-75 block"
             loading="lazy"
             width="270"
             format="webp"
             placeholder
           />
        </div>

        <div 
          class="flex justify-end mt-2 px-1 relative z-20"
          @mouseenter="openMenuId = img.id"
          @mouseleave="openMenuId = null"
        >
          <button 
              @click.stop="toggleMenu(img.id)" 
              class="w-8 h-8 flex items-center justify-center text-gray-900 hover:text-gray-600 transition-colors duration-200 focus:outline-none rounded-full hover:bg-gray-100"
              title="Más opciones"
              >
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24" aria-hidden="true">
                <path d="M12 8c1.1 0 2-.9 2-2s-.9-2-2-2-2 .9-2 2 .9 2 2 2zm0 2c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2zm0 6c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2z"></path>
              </svg>
          </button>

          <!-- Menú Flotante -->
          <transition 
              enter-active-class="transition ease-out duration-100"
              enter-from-class="transform opacity-0 scale-95"
              enter-to-class="transform opacity-100 scale-100"
              leave-active-class="transition ease-in duration-75"
              leave-from-class="transform opacity-100 scale-100"
              leave-to-class="transform opacity-0 scale-95"
           >
              <div 
                  v-if="openMenuId === img.id" 
                  class="absolute right-0 bottom-full mb-2 bg-white shadow-[0_0_20px_rgba(0,0,0,0.15)] rounded-2xl py-2 z-50 w-52 sm:w-[260px] text-left origin-bottom-right"
                  @click.stop
              >
                  <button 
                          @click="downloadAndClose(img)"
                          class="w-full text-left px-3 sm:px-4 py-3 hover:bg-gray-100 flex items-center gap-2 sm:gap-3 transition-colors group"
                      >
                          <span class="flex-shrink-0 w-6 h-6 flex items-center justify-center">
                              <svg style="width: 20px; height: 20px;" viewBox="0 0 24 24" fill="currentColor" class="text-gray-900"><path d="M16.3 7.8 13 11.08V0h-2v11.09l-3.3-3.3-1.4 1.42 5.7 5.7 5.7-5.7zM2 18v-5H0v5a4 4 0 0 0 4 4h16a4 4 0 0 0 4-4v-5h-2v5a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2"></path></svg>
                          </span>
                          <span class="text-sm sm:text-[16px] font-bold text-gray-900">Descargar imagen</span>
                      </button>
              </div>
          </transition>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useBreakpoints, breakpointsTailwind } from '@vueuse/core'

const props = defineProps({
  images: Array
})

const emit = defineEmits(['open', 'download'])

const openMenuId = ref(null)

// --- Masonry Logic ---
const breakpoints = useBreakpoints(breakpointsTailwind)

const colCount = computed(() => {
  if (breakpoints.xl.value) return 6
  if (breakpoints.md.value) return 4
  return 2
})

const columns = computed(() => {
  // Initialize empty arrays for each column
  const cols = Array.from({ length: colCount.value }, () => [])
  
  if (!props.images) return cols

  // Distribute images into columns
  props.images.forEach((img, index) => {
    const colIndex = index % colCount.value
    cols[colIndex].push(img)
  })

  return cols
})
// ---------------------

const toggleMenu = (id) => {
  if (openMenuId.value === id) {
    openMenuId.value = null
  } else {
    openMenuId.value = id
  }
}

const closeMenu = () => {
  openMenuId.value = null
}

const downloadAndClose = (img) => {
  emit('download', img)
  closeMenu()
}
</script>