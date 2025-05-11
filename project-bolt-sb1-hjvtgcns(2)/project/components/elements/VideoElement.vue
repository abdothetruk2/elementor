<template>
  <div :style="containerStyle">
    <video 
      :src="element.content?.url" 
      :style="videoStyle"
      :controls="element.settings?.controls"
      :autoplay="element.settings?.autoplay"
      :muted="element.settings?.muted"
      :loop="element.settings?.loop"
    ></video>
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

// Video styles
const videoStyle = computed(() => {
  const { settings } = props.element;
  const { width, height, widthUnit, heightUnit } = settings;
  
  return {
    width: width ? `${width}${widthUnit || '%'}` : '100%',
    height: height ? `${height}${heightUnit || 'px'}` : 'auto',
    maxWidth: '100%'
  };
});
</script>