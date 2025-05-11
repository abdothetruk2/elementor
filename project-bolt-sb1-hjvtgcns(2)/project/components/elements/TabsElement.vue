<template>
  <div :style="containerStyle" class="tabs-element">
    <!-- Tab Headers -->
    <div 
      class="flex border-b"
      :style="{ borderColor: element.settings.tabs?.borderColor || '#e5e7eb' }"
    >
      <button
        v-for="(tab, index) in element.content.tabs"
        :key="tab.id"
        @click="activeTab = index"
        class="px-4 py-2 -mb-px"
        :class="[
          activeTab === index 
            ? 'border-b-2 text-primary border-primary' 
            : 'text-gray-500 hover:text-gray-700'
        ]"
        :style="tabHeaderStyle"
      >
        {{ tab.title }}
      </button>
    </div>

    <!-- Tab Content -->
    <div class="py-4" :style="tabContentStyle">
      <div
        v-for="(tab, index) in element.content.tabs"
        :key="tab.id"
        v-show="activeTab === index"
      >
        {{ tab.content }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  element: {
    type: Object,
    required: true
  }
});

const activeTab = ref(0);

const containerStyle = computed(() => {
  const { settings } = props.element;
  const { margin, padding } = settings;
  
  return {
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`
  };
});

const tabHeaderStyle = computed(() => {
  const { settings } = props.element;
  const { tabs } = settings;
  
  return {
    fontSize: tabs?.fontSize || '14px',
    fontWeight: tabs?.fontWeight || '500'
  };
});

const tabContentStyle = computed(() => {
  const { settings } = props.element;
  const { content } = settings;
  
  return {
    color: content?.color || '#374151',
    fontSize: content?.fontSize || '14px',
    lineHeight: content?.lineHeight || '1.5'
  };
});
</script>