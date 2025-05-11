<template>
  <nav :style="containerStyle" class="navbar-element">
    <div class="container mx-auto px-4">
      <div class="flex justify-between items-center h-16">
        <!-- Logo -->
        <div class="flex-shrink-0">
          <NuxtLink to="/" class="flex items-center">
            <img 
              v-if="element.content?.logo?.url" 
              :src="element.content.logo.url" 
              :alt="element.content.logo.alt"
              :style="{ height: element.settings.logoHeight + 'px' }"
            />
            <span v-else class="text-xl font-bold">{{ element.content.title || 'Logo' }}</span>
          </NuxtLink>
        </div>

        <!-- Desktop Menu -->
        <div class="hidden md:flex space-x-8">
          <NuxtLink 
            v-for="item in element.content.menuItems" 
            :key="item.id"
            :to="item.url"
            :style="menuItemStyle"
            class="transition-colors duration-200 hover:text-primary"
          >
            {{ item.text }}
          </NuxtLink>
        </div>

        <!-- Mobile Menu Button -->
        <div class="md:hidden">
          <button 
            @click="mobileMenuOpen = !mobileMenuOpen"
            class="text-gray-500 hover:text-gray-600 focus:outline-none"
          >
            <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path v-if="!mobileMenuOpen" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
              <path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>
      </div>

      <!-- Mobile Menu -->
      <div 
        v-show="mobileMenuOpen" 
        class="md:hidden"
      >
        <div class="px-2 pt-2 pb-3 space-y-1">
          <NuxtLink 
            v-for="item in element.content.menuItems" 
            :key="item.id"
            :to="item.url"
            :style="mobileMenuItemStyle"
            class="block px-3 py-2 rounded-md text-base font-medium transition-colors duration-200 hover:text-primary"
          >
            {{ item.text }}
          </NuxtLink>
        </div>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  element: {
    type: Object,
    required: true
  }
});

const mobileMenuOpen = ref(false);

const containerStyle = computed(() => {
  const { settings } = props.element;
  const { margin, padding, background, sticky } = settings;
  
  return {
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`,
    backgroundColor: background?.color || '#ffffff',
    position: sticky ? 'sticky' : 'relative',
    top: sticky ? '0' : 'auto',
    zIndex: sticky ? '50' : 'auto',
    borderBottom: settings.border?.show ? `1px solid ${settings.border.color || '#e5e7eb'}` : 'none'
  };
});

const menuItemStyle = computed(() => {
  const { settings } = props.element;
  const { menuItems } = settings;
  
  return {
    color: menuItems?.color || '#374151',
    fontSize: menuItems?.fontSize || '16px',
    fontWeight: menuItems?.fontWeight || '500'
  };
});

const mobileMenuItemStyle = computed(() => ({
  ...menuItemStyle.value,
  backgroundColor: 'transparent',
  ':hover': {
    backgroundColor: '#f3f4f6'
  }
}));
</script>