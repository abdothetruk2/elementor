<template>
  <div :style="containerStyle" class="sidebar-element">
    <div class="flex">
      <!-- Sidebar -->
      <div 
        :style="sidebarStyle"
        class="flex flex-col"
      >
        <div v-if="element.content.title" class="p-4 border-b" :style="{ borderColor: element.settings.borderColor }">
          <h3 class="font-medium" :style="{ color: element.settings.titleColor }">{{ element.content.title }}</h3>
        </div>
        
        <div class="flex-1">
          <nav class="space-y-1">
            <a 
              v-for="item in element.content.items" 
              :key="item.id"
              :href="item.url"
              class="block px-4 py-2 transition-colors duration-200"
              :style="linkStyle"
            >
              {{ item.text }}
            </a>
          </nav>
        </div>
      </div>

      <!-- Content -->
      <div class="flex-1 p-4">
        <slot></slot>
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

const sidebarStyle = computed(() => {
  const { settings } = props.element;
  return {
    width: settings.width || '250px',
    backgroundColor: settings.backgroundColor || '#ffffff',
    borderRight: `1px solid ${settings.borderColor || '#e5e7eb'}`
  };
});

const linkStyle = computed(() => {
  const { settings } = props.element;
  return {
    color: settings.linkColor || '#374151',
    ':hover': {
      backgroundColor: settings.hoverBgColor || '#f3f4f6',
      color: settings.hoverColor || '#111827'
    }
  };
});
</script>