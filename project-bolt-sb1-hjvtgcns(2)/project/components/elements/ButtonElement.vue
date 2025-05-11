<template>
  <div :style="containerStyle">
    <a 
      :href="element.content.url" 
      :target="element.content.target || '_self'"
      :style="buttonStyle"
      class="inline-block transition-colors duration-200 text-center"
    >
      {{ element.content.text }}
    </a>
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

// Container styles for alignment
const containerStyle = computed(() => {
  const { settings } = props.element;
  const { margin, padding, align } = settings;
  
  return {
    textAlign: align || 'left',
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`
  };
});

// Button styles
const buttonStyle = computed(() => {
  const { settings } = props.element;
  const { typography, type, size } = settings;
  
  // Size settings
  const sizeStyles = {
    small: { padding: '6px 12px', fontSize: '14px' },
    medium: { padding: '8px 16px', fontSize: '16px' },
    large: { padding: '10px 20px', fontSize: '18px' }
  };
  
  // Type settings
  const typeStyles = {
    primary: { backgroundColor: 'var(--color-primary)', color: '#ffffff', border: 'none', borderRadius: '4px' },
    secondary: { backgroundColor: 'var(--color-secondary)', color: '#ffffff', border: 'none', borderRadius: '4px' },
    outline: { backgroundColor: 'transparent', color: 'var(--color-primary)', border: '1px solid var(--color-primary)', borderRadius: '4px' },
    link: { backgroundColor: 'transparent', color: 'var(--color-primary)', border: 'none', textDecoration: 'underline' }
  };
  
  const selectedSize = sizeStyles[size || 'medium'];
  const selectedType = typeStyles[type || 'primary'];
  
  return {
    ...selectedSize,
    ...selectedType,
    fontWeight: typography?.weight || '500',
    lineHeight: typography?.lineHeight || '1.2'
  };
});
</script>