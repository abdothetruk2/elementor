<template>
  <div 
    v-if="element && element.type"
    :class="['element-container relative my-2 editor-element group', selected ? 'selected' : '']"
    @click.stop="selectElement"
    @dragover.prevent
    @drop.stop="$emit('drop', $event)"
  >
    <component 
      :is="elementComponent" 
      :element="element" 
      :key="element.id"
      v-if="element && elementComponent"
    />
    
    <!-- Element Controls -->
    <div 
      v-if="selected && element" 
      class="element-controls absolute top-0 left-0 transform -translate-y-full bg-green-500 text-white flex items-center text-xs rounded-t-md overflow-hidden z-10"
    >
      <div class="p-1 px-2 font-medium capitalize">{{ element.type }}</div>
      <div class="flex">
        <div class="relative">
          <button 
            @click.stop="showReplaceMenu = !showReplaceMenu" 
            class="p-1 px-2 hover:bg-green-600"
            title="Replace Element"
          >
            Replace
          </button>
          <div 
            v-if="showReplaceMenu" 
            class="absolute top-full left-0 mt-1 bg-white shadow-lg rounded-md p-2 z-20 w-48"
          >
            <div 
              v-for="widget in availableWidgets" 
              :key="widget.type"
              @click.stop="replaceElement(widget)"
              class="text-gray-700 hover:bg-gray-100 p-2 rounded-md flex items-center cursor-pointer"
            >
              <span>{{ widget.label }}</span>
            </div>
          </div>
        </div>
        <button 
          @click.stop="moveElement('up')"
          class="p-1 px-2 hover:bg-green-600" 
          title="Move Up"
        >
          ↑
        </button>
        <button 
          @click.stop="moveElement('down')"
          class="p-1 px-2 hover:bg-green-600" 
          title="Move Down"
        >
          ↓
        </button>
        <button 
          class="p-1 px-2 hover:bg-green-600 text-white" 
          title="Delete Element"
          @click.stop="deleteElement"
        >
          Delete
        </button>
      </div>
    </div>
    
    <!-- Handle for dragging (visible on hover) -->
    <div 
      v-if="element"
      class="drag-handle absolute left-0 top-1/2 transform -translate-y-1/2 -translate-x-full bg-gray-200 p-1 rounded-l cursor-move opacity-0 group-hover:opacity-100 transition-opacity"
      :class="{ 'opacity-100': selected }"
      title="Drag to reorder"
    >
      <div class="w-4 h-6 flex flex-col justify-center space-y-1">
        <div class="w-full h-0.5 bg-gray-500"></div>
        <div class="w-full h-0.5 bg-gray-500"></div>
        <div class="w-full h-0.5 bg-gray-500"></div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, markRaw } from 'vue';

// Import element components
import HeadingElement from '~/components/elements/HeadingElement.vue';
import TextElement from '~/components/elements/TextElement.vue';
import ButtonElement from '~/components/elements/ButtonElement.vue';
import ImageElement from '~/components/elements/ImageElement.vue';
import DividerElement from '~/components/elements/DividerElement.vue';
import SpacerElement from '~/components/elements/SpacerElement.vue';
import NavbarElement from '~/components/elements/NavbarElement.vue';
import AccordionElement from '~/components/elements/AccordionElement.vue';
import CodeElement from '~/components/elements/CodeElement.vue';
import FormElement from '~/components/elements/FormElement.vue';
import GalleryElement from '~/components/elements/GalleryElement.vue';
import TabsElement from '~/components/elements/TabsElement.vue';
import VideoElement from '~/components/elements/VideoElement.vue';
import SocialElement from '~/components/elements/SocialElement.vue';

const props = defineProps({
  element: {
    type: Object,
    required: true
  },
  index: {
    type: Number,
    required: true
  }
});

const emit = defineEmits(['select', 'delete', 'drop', 'replace', 'move']);

const selected = ref(false);
const showReplaceMenu = ref(false);

// Available widgets for replacement
const availableWidgets = [
  { type: 'heading', label: 'Heading' },
  { type: 'text', label: 'Text' },
  { type: 'image', label: 'Image' },
  { type: 'button', label: 'Button' },
  { type: 'spacer', label: 'Spacer' },
  { type: 'divider', label: 'Divider' },
  { type: 'navbar', label: 'Navigation' },
  { type: 'accordion', label: 'Accordion' },
  { type: 'code', label: 'Code' },
  { type: 'form', label: 'Form' },
  { type: 'gallery', label: 'Gallery' },
  { type: 'tabs', label: 'Tabs' },
  { type: 'video', label: 'Video' },
  { type: 'social', label: 'Social' }
];

// Use markRaw to prevent Vue from making the components reactive
const componentMap = {
  heading: markRaw(HeadingElement),
  text: markRaw(TextElement),
  button: markRaw(ButtonElement),
  image: markRaw(ImageElement),
  divider: markRaw(DividerElement),
  spacer: markRaw(SpacerElement),
  navbar: markRaw(NavbarElement),
  accordion: markRaw(AccordionElement),
  code: markRaw(CodeElement),
  form: markRaw(FormElement),
  gallery: markRaw(GalleryElement),
  tabs: markRaw(TabsElement),
  video: markRaw(VideoElement),
  social: markRaw(SocialElement)
};

// Determine which component to use based on element type
const elementComponent = computed(() => {
  if (!props.element?.type) {
    console.warn('Element or element type is undefined');
    return null;
  }
  
  const component = componentMap[props.element.type];
  if (!component) {
    console.warn(`No component found for element type: ${props.element.type}`);
    return null;
  }
  return component;
});

// Close dropdown when clicking outside
const handleClickOutside = (event) => {
  if (showReplaceMenu.value) {
    showReplaceMenu.value = false;
  }
};

// Setup and cleanup event listener
onMounted(() => {
  document.addEventListener('click', handleClickOutside);
});

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside);
});

// Select this element
function selectElement() {
  if (!props.element) return;
  selected.value = true;
  emit('select', props.element);
}

function deleteElement() {
  if (!props.element?.id) return;
  emit('delete', props.element.id);
}

// Replace current element with a new type
function replaceElement(widget) {
  if (!props.element?.id) return;
  emit('replace', props.element.id, widget.type);
  showReplaceMenu.value = false;
}

// Move element up or down
function moveElement(direction) {
  if (!props.element?.id) return;
  emit('move', props.element.id, direction);
}
</script>

<style scoped>
.element-container {
  position: relative;
  transition: all 0.2s ease;
}

.element-container.drag-over {
  border: 2px dashed var(--color-primary);
}

.drag-handle {
  z-index: 8;
  left: -10px;
}
</style>