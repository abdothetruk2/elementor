<template>
  <div :style="containerStyle">
    <hr :style="dividerStyle" />
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
  const { margin, padding } = settings;
  
  return {
    margin: `${margin?.top || '20px'} ${margin?.right || '0px'} ${margin?.bottom || '20px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`
  };
});

// Divider styles
const dividerStyle = computed(() => {
  const { settings } = props.element;
  const { width, style, color } = settings;
  
  return {
    width: width || '100%',
    borderWidth: '0',
    borderTopWidth: '1px',
    borderStyle: style || 'solid',
    borderColor: color || '#e5e7eb'
  };
});
</script>