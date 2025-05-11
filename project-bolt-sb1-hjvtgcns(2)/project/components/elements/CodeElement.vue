<template>
  <div :style="containerStyle">
    <div v-if="element.settings.type === 'html'" v-html="sanitizedHTML"></div>
    <pre v-else :class="['code-block', `language-${element.settings.type}`]">
      <code>{{ element.content }}</code>
    </pre>
  </div>
</template>

<script setup>
import { computed } from 'vue';
import DOMPurify from 'dompurify';

const props = defineProps({
  element: {
    type: Object,
    required: true
  }
});

// Container styles
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

// Sanitize HTML content
const sanitizedHTML = computed(() => {
  if (props.element.settings.type === 'html') {
    return DOMPurify.sanitize(props.element.content);
  }
  return '';
});
</script>

<style scoped>
.code-block {
  background: #1e1e1e;
  color: #d4d4d4;
  padding: 1rem;
  border-radius: 4px;
  overflow-x: auto;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', 'Consolas', monospace;
  font-size: 14px;
  line-height: 1.5;
}
</style>