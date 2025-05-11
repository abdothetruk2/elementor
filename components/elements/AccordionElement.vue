<template>
  <div :style="containerStyle" class="accordion-element">
    <div v-for="(item, index) in element.content.items" :key="item.id" class="accordion-item">
      <button
        class="w-full flex justify-between items-center p-4 focus:outline-none"
        :class="[activeIndex === index ? 'bg-gray-50' : 'bg-white']"
        @click="toggleItem(index)"
        :style="headerStyle"
      >
        <span>{{ item.title }}</span>
        <svg
          class="w-5 h-5 transform transition-transform duration-200"
          :class="{ 'rotate-180': activeIndex === index }"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
        </svg>
      </button>
      
      <div
        v-show="activeIndex === index"
        class="p-4"
        :style="contentStyle"
      >
        {{ item.content }}
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

const activeIndex = ref(0);

const containerStyle = computed(() => {
  const { settings } = props.element;
  const { margin, padding, border } = settings;
  
  return {
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`,
    border: border?.show ? `${border.width}px solid ${border.color}` : 'none',
    borderRadius: settings.borderRadius || '4px'
  };
});

const headerStyle = computed(() => {
  const { settings } = props.element;
  const { header } = settings;
  
  return {
    color: header?.color || '#111827',
    fontSize: header?.fontSize || '16px',
    fontWeight: header?.fontWeight || '500'
  };
});

const contentStyle = computed(() => {
  const { settings } = props.element;
  const { content } = settings;
  
  return {
    color: content?.color || '#374151',
    fontSize: content?.fontSize || '14px',
    lineHeight: content?.lineHeight || '1.5'
  };
});

function toggleItem(index) {
  activeIndex.value = activeIndex.value === index ? -1 : index;
}
</script>