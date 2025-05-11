<template>
  <component 
    :is="element.settings?.tag || 'h2'" 
    :style="headingStyle"
  >
    {{ element.content }}
  </component>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  element: {
    type: Object,
    required: true
  }
});

// Compute heading styles based on element settings
const headingStyle = computed(() => {
  const { settings = {} } = props.element;
  const { typography = {}, color, margin = {}, padding = {}, align } = settings;
  
  return {
    color: color || '#333333',
    fontSize: typography?.size || '28px',
    fontWeight: typography?.weight || '600',
    fontFamily: typography?.family || 'Inter',
    lineHeight: typography?.lineHeight || '1.2',
    textAlign: align || 'left',
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`
  };
});
</script>