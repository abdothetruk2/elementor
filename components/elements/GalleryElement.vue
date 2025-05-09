<template>
  <div :style="containerStyle">
    <div 
      class="gallery-grid"
      :style="{
        display: 'grid',
        gridTemplateColumns: `repeat(${element.settings.columns}, 1fr)`,
        gap: `${element.settings.gap}px`
      }"
    >
      <div 
        v-for="(image, index) in element.settings.images" 
        :key="index"
        class="gallery-item relative group"
        @click="element.settings.lightbox && openLightbox(index)"
      >
        <img 
          :src="image.url" 
          :alt="image.alt"
          class="w-full h-full object-cover rounded-md transition-transform duration-200 group-hover:scale-105"
        />
        <div 
          v-if="image.caption"
          class="absolute bottom-0 left-0 right-0 bg-black bg-opacity-50 text-white p-2 text-sm opacity-0 group-hover:opacity-100 transition-opacity duration-200"
        >
          {{ image.caption }}
        </div>
      </div>
    </div>
    
    <!-- Lightbox -->
    <div 
      v-if="lightboxOpen"
      class="fixed inset-0 bg-black bg-opacity-90 z-50 flex items-center justify-center"
      @click="closeLightbox"
    >
      <button 
        class="absolute top-4 right-4 text-white text-2xl"
        @click="closeLightbox"
      >
        ×
      </button>
      <button 
        class="absolute left-4 top-1/2 transform -translate-y-1/2 text-white text-4xl"
        @click.stop="previousImage"
      >
        ‹
      </button>
      <button 
        class="absolute right-4 top-1/2 transform -translate-y-1/2 text-white text-4xl"
        @click.stop="nextImage"
      >
        ›
      </button>
      <img 
        :src="currentImage?.url" 
        :alt="currentImage?.alt"
        class="max-h-[90vh] max-w-[90vw] object-contain"
      />
      <div 
        v-if="currentImage?.caption"
        class="absolute bottom-4 left-1/2 transform -translate-x-1/2 bg-black bg-opacity-50 text-white p-2 rounded"
      >
        {{ currentImage.caption }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  element: {
    type: Object,
    required: true
  }
});

const lightboxOpen = ref(false);
const currentImageIndex = ref(0);

const containerStyle = computed(() => {
  const { settings } = props.element;
  const { margin, padding, width, height, widthUnit, heightUnit } = settings;
  
  return {
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`,
    width: width ? `${width}${widthUnit || '%'}` : '100%',
    height: height ? `${height}${heightUnit || 'px'}` : 'auto'
  };
});

const currentImage = computed(() => {
  return props.element.settings.images[currentImageIndex.value];
});

function openLightbox(index) {
  currentImageIndex.value = index;
  lightboxOpen.value = true;
}

function closeLightbox() {
  lightboxOpen.value = false;
}

function previousImage(event) {
  event.stopPropagation();
  currentImageIndex.value = (currentImageIndex.value - 1 + props.element.settings.images.length) % props.element.settings.images.length;
}

function nextImage(event) {
  event.stopPropagation();
  currentImageIndex.value = (currentImageIndex.value + 1) % props.element.settings.images.length;
}
</script>