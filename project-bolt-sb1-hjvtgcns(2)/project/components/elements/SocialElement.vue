<template>
  <div :style="containerStyle">
    <div 
      class="social-icons"
      :style="{ textAlign: element.settings?.align || 'left' }"
    >
      <a 
        v-for="network in enabledNetworks" 
        :key="network.type"
        :href="network.url"
        target="_blank"
        rel="noopener noreferrer"
        class="inline-block transition-opacity hover:opacity-80"
        :style="{ 
          marginRight: element.settings?.iconSpacing || '16px',
          width: element.settings?.iconSize || '24px',
          height: element.settings?.iconSize || '24px'
        }"
      >
        <img 
          :src="getSocialIcon(network.type)"
          :alt="network.type"
          class="w-full h-full"
        />
      </a>
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
  const settings = props.element.settings || {};
  const margin = settings.margin || {};
  const padding = settings.padding || {};
  
  return {
    margin: `${margin.top || '0px'} ${margin.right || '0px'} ${margin.bottom || '0px'} ${margin.left || '0px'}`,
    padding: `${padding.top || '0px'} ${padding.right || '0px'} ${padding.bottom || '0px'} ${padding.left || '0px'}`,
    width: settings.width ? `${settings.width}${settings.widthUnit || '%'}` : '100%',
    height: settings.height ? `${settings.height}${settings.heightUnit || 'px'}` : 'auto'
  };
});

const enabledNetworks = computed(() => {
  return props.element.settings?.networks?.filter(network => network.enabled) || [];
});

function getSocialIcon(type) {
  const icons = {
    facebook: 'https://raw.githubusercontent.com/simple-icons/simple-icons/develop/icons/facebook.svg',
    twitter: 'https://raw.githubusercontent.com/simple-icons/simple-icons/develop/icons/twitter.svg',
    instagram: 'https://raw.githubusercontent.com/simple-icons/simple-icons/develop/icons/instagram.svg',
    linkedin: 'https://raw.githubusercontent.com/simple-icons/simple-icons/develop/icons/linkedin.svg'
  };
  
  return icons[type] || '';
}
</script>