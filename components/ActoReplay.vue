<template>
  <div class="max-w-5xl mx-auto bg-white rounded-3xl p-4 md:p-8 shadow-sm text-center">
    <div class="mb-4 md:mb-8">
      <div class="w-16 h-16 md:w-20 md:h-20 bg-blue-50 rounded-full flex items-center justify-center mx-auto mb-4 text-blue-600">
         <svg class="w-8 h-8 md:w-10 md:h-10" fill="none" viewBox="0 0 24 24" stroke="currentColor">
           <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z" />
         </svg>
      </div>
      <h2 class="text-xl md:text-2xl font-bold text-gray-900 mb-2">Repetición del Acto de Grado</h2>
    </div>
    
    <div class="bg-gray-900 rounded-2xl overflow-hidden shadow-lg mb-6">
       <iframe 
         src="https://drive.google.com/file/d/12AxWAaG7blxtzt2BhnTLGYeYZVCs6ggI/preview" 
         class="w-full aspect-video" 
         allow="autoplay" 
         allowfullscreen
       ></iframe>
    </div>

    <div class="flex justify-center">
      <a :href="driveUrl" target="_blank" class="inline-flex items-center gap-2 px-6 py-3 bg-blue-50 text-blue-700 rounded-xl hover:bg-blue-100 transition-colors font-medium">
        <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" />
        </svg>
        Ver en Google Drive (Respaldo)
      </a>
    </div>
  </div>
</template>

<script setup>
const driveUrl = 'https://drive.google.com/drive/folders/1ZExElrO21XbYNTKe5Mq--UtVX2gn6kG2';
</script>
