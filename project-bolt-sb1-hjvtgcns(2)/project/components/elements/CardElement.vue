<template>
  <div :style="containerStyle" class="card-element">
    <div class="bg-white rounded-lg shadow-md overflow-hidden" :style="cardStyle">
      <img v-if="element.content?.image" :src="element.content.image" :alt="element.content.imageAlt" class="w-full h-48 object-cover">
      <div class="p-6">
        <h3 class="text-xl font-semibold mb-2">{{ element.content?.title }}</h3>
        <p class="text-gray-600">{{ element.content?.description }}</p>
        <div v-if="element.content?.buttonText" class="mt-4">
          <a :href="element.content?.buttonUrl" class="btn btn-primary">{{ element.content?.buttonText }}</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  element: {
    type: Object,
    required: true
  }
});

const containerStyle = computed(() => {
  const { settings } = props.element;
  const { margin, padding } = settings;
  
  return {
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`
  };
});

const cardStyle = computed(() => {
  const { settings } = props.element;
  return {
    backgroundColor: settings.backgroundColor || '#ffffff',
    boxShadow: settings.shadow || '0 1px 3px 0 rgb(0 0 0 / 0.1)',
    borderRadius: settings.borderRadius || '0.5rem'
  };
});
</script>