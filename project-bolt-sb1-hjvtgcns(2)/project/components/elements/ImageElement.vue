<template>
  <div :style="containerStyle">
    <img 
      :src="element.content?.url" 
      :alt="element.content?.alt || ''"
      :style="imageStyle"
    />
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

// Container styles
const containerStyle = computed(() => {
  const { settings } = props.element;
  const { margin, padding, align } = settings;
  
  return {
    textAlign: align || 'left',
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`
  };
});

// Image styles
const imageStyle = computed(() => {
  const { settings } = props.element;
  const { width, height, objectFit, borderRadius } = settings;
  
  return {
    width: width || '100%',
    height: height || 'auto',
    objectFit: objectFit || 'cover',
    borderRadius: borderRadius || '0px'
  };
});
</script>