<template>
  <div :style="containerStyle" class="alert-element">
    <div 
      class="p-4 rounded-lg"
      :style="alertStyle"
    >
      <div class="flex items-center">
        <div v-if="element.settings.showIcon" class="flex-shrink-0 mr-3">
          <svg class="h-5 w-5" :style="{ color: element.settings.iconColor }" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path v-if="element.settings.type === 'success'" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
            <path v-else-if="element.settings.type === 'warning'" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
            <path v-else-if="element.settings.type === 'error'" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            <path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
        </div>
        <div>
          <h4 v-if="element.content.title" class="text-lg font-medium mb-1" :style="{ color: element.settings.titleColor }">
            {{ element.content.title }}
          </h4>
          <p :style="{ color: element.settings.textColor }">{{ element.content.message }}</p>
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

const alertStyle = computed(() => {
  const { settings } = props.element;
  const typeColors = {
    info: { bg: '#EFF6FF', border: '#BFDBFE' },
    success: { bg: '#F0FDF4', border: '#BBF7D0' },
    warning: { bg: '#FFFBEB', border: '#FDE68A' },
    error: { bg: '#FEF2F2', border: '#FECACA' }
  };
  
  const colors = typeColors[settings.type] || typeColors.info;
  
  return {
    backgroundColor: settings.backgroundColor || colors.bg,
    borderColor: settings.borderColor || colors.border,
    borderWidth: settings.borderWidth || '1px',
    borderStyle: 'solid'
  };
});
</script>