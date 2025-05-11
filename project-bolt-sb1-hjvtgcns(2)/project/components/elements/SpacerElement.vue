<template>
  <div :style="spacerStyle"></div>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  element: {
    type: Object,
    required: true
  }
});

// Spacer styles
const spacerStyle = computed(() => {
  const { settings } = props.element;
  const height = settings.height || '40px';
  const { margin, padding } = settings;
  const mobileVisible = settings.mobileVisible !== false;
  
  return {
    height: height,
    width: '100%',
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`,
    display: mobileVisible ? 'block' : 'none',
    '@media (min-width: 768px)': {
      display: 'block'
    }
  };
});
</script>