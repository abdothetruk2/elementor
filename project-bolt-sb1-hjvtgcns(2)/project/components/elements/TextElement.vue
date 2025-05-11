<template>
  <p :style="textStyle">
    {{ element.content }}
  </p>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  element: {
    type: Object,
    required: true
  }
});

// Compute text styles based on element settings
const textStyle = computed(() => {
  const { settings } = props.element;
  const { typography, color, margin, padding, align } = settings;
  
  return {
    color: color || '#666666',
    fontSize: typography?.size || '16px',
    fontWeight: typography?.weight || '400',
    lineHeight: typography?.lineHeight || '1.6',
    textAlign: align || 'left',
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`
  };
});
</script>