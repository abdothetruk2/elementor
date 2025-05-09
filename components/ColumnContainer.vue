<template>
  <div 
    class="column-container relative"
    @dragover.prevent
    @drop="handleDrop"
  >
    <div class="column-content min-h-[100px]">
      <draggable 
        v-model="columnElements"
        group="elements"
        :item-key="'id'" 
        :animation="200"
        ghost-class="ghost"
        handle=".drag-handle"
        @start="dragging = true"
        @end="dragging = false"
        @add="handleAdd"
        @update:modelValue="updateElements"
        :elements="column.elements"
      >
        <template #item="{ element }">
          <ElementContainer 
            :element="element"
            :index="element.index"
            :key="element.id"
            :selectedElementId="selectedElementId"
            @select="selectElement"
            @delete="deleteElement"
            @replace="replaceElement"
            @move="moveElement"
            @dragover.prevent
            @drop.stop="(event) => handleReplace(event, element)"
          />
        </template>
      </draggable>
      
      <div 
        v-if="column.elements.length === 0" 
        class="empty-column flex items-center justify-center p-4 h-32 border border-dashed border-gray-300 rounded"
      >
        <p class="text-sm text-gray-400">Drag widgets here</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import draggable from 'vuedraggable';
import { v4 as uuidv4 } from 'uuid';

const props = defineProps({
  column: {
    type: Object,
    required: true
  },
  index: {
    type: Number,
    required: true
  },
  selectedElementId: {
    type: String,
    default: null
  }
});

const emit = defineEmits(['select', 'update', 'delete']);

const dragging = ref(false);

const columnElements = computed({
  get: () => props.column.elements,
  set: (newElements) => {
    emit('update', { ...props.column, elements: newElements });
  }
});

function handleDrop(event) {
  try {
    const data = event.dataTransfer.getData('application/json');
    if (!data) {
      console.warn('No data received in drop event');
      return;
    }
    
    const widgetData = JSON.parse(data);
    const newElement = {
      ...widgetData,
      id: uuidv4(),
      settings: widgetData.settings || getDefaultSettings(widgetData.type)
    };
    
    emit('update', { ...props.column, elements: [...props.column.elements, newElement] });
  } catch (error) {
    console.error('Error processing drop event:', error);
  }
}

function handleReplace(event, targetElement) {
  try {
    const data = event.dataTransfer.getData('application/json');
    if (!data) {
      console.warn('No data received in drop event');
      return;
    }
    
    const widgetData = JSON.parse(data);
    const newElement = {
      ...widgetData,
      id: uuidv4(),
      settings: {
        ...getDefaultSettings(widgetData.type),
        ...targetElement.settings // Preserve existing element's settings
      }
    };
    
    // Find the target element index and replace it
    const elements = [...props.column.elements];
    const targetIndex = elements.findIndex(el => el.id === targetElement.id);
    
    if (targetIndex !== -1) {
      elements[targetIndex] = newElement;
      emit('update', { ...props.column, elements });
    }
  } catch (error) {
    console.error('Error processing replace event:', error);
  }
}

function handleAdd(event) {
  // This is called when an element is added via draggable
  // We can use this to ensure the new element has a unique ID
  const newElement = event.item.__draggable_context.element;
  if (newElement && !newElement.id) {
    newElement.id = uuidv4();
  }
  
  // Ensure settings are defined
  if (newElement && !newElement.settings) {
    newElement.settings = getDefaultSettings(newElement.type);
  }
}

function updateElements(newElements) {
  emit('update', { ...props.column, elements: newElements });
}

function selectElement(element) {
  emit('select', element);
}

function deleteElement(elementId) {
  emit('delete', elementId, props.column.id);
}

// Handle element replacement through the UI menu
function replaceElement(elementId, newType) {
  // Find the existing element
  const elements = [...props.column.elements];
  const targetIndex = elements.findIndex(el => el.id === elementId);
  
  if (targetIndex !== -1) {
    const existingElement = elements[targetIndex];
    
    // Create new element with preserved settings where appropriate
    const newElement = {
      id: uuidv4(),
      type: newType,
      settings: {
        ...getDefaultSettings(newType),
        margin: existingElement.settings?.margin || { top: '0px', right: '0px', bottom: '0px', left: '0px' },
        padding: existingElement.settings?.padding || { top: '0px', right: '0px', bottom: '0px', left: '0px' },
      },
      content: getDefaultContent(newType)
    };
    
    // Replace the element
    elements[targetIndex] = newElement;
    emit('update', { ...props.column, elements });
  }
}

// Move element up or down in the column
function moveElement(elementId, direction) {
  const elements = [...props.column.elements];
  const currentIndex = elements.findIndex(el => el.id === elementId);
  
  if (currentIndex === -1) return;
  
  let newIndex;
  if (direction === 'up') {
    newIndex = Math.max(0, currentIndex - 1);
  } else {
    newIndex = Math.min(elements.length - 1, currentIndex + 1);
  }
  
  if (newIndex === currentIndex) return;
  
  // Swap elements
  [elements[currentIndex], elements[newIndex]] = [elements[newIndex], elements[currentIndex]];
  
  emit('update', { ...props.column, elements });
}

// Default settings for new elements
function getDefaultSettings(type) {
  const commonSettings = {
    margin: { top: '0px', right: '0px', bottom: '0px', left: '0px' },
    padding: { top: '0px', right: '0px', bottom: '0px', left: '0px' }
  };
  
  const typeSpecificSettings = {
    heading: {
      tag: 'h2',
      align: 'left',
      color: '#333333',
      typography: { size: '28px', weight: '600', family: 'Inter', lineHeight: '1.2' },
      border: { style: 'none', width: '0', color: '#cccccc' }
    },
    text: {
      align: 'left',
      color: '#666666',
      typography: { size: '16px', weight: '400', family: 'Inter', lineHeight: '1.6' },
      border: { style: 'none', width: '0', color: '#cccccc' }
    },
    button: {
      size: 'medium',
      type: 'primary',
      align: 'left',
      typography: { size: '16px', weight: '500', family: 'Inter', lineHeight: '1.2' },
      border: { style: 'none', width: '0', color: '#cccccc' }
    },
    image: {
      width: '100%',
      height: 'auto',
      objectFit: 'cover',
      borderRadius: '0px',
      border: { style: 'none', width: '0', color: '#cccccc' }
    },
    divider: {
      width: '100%',
      style: 'solid',
      color: '#e5e7eb'
    },
    spacer: {
      height: '40px',
      mobileVisible: true
    }
  };
  
  return { ...commonSettings, ...(typeSpecificSettings[type] || {}) };
}

// Default content for new elements
function getDefaultContent(type) {
  const defaults = {
    heading: 'Add Your Heading Text Here',
    text: 'Add your text content here. Edit the text to add your own content.',
    button: {
      text: 'Click Here',
      url: '#',
      target: '_self'
    },
    image: {
      url: 'https://images.pexels.com/photos/1181271/pexels-photo-1181271.jpeg',
      alt: 'Image description'
    }
  };
  
  return defaults[type] || {};
}
</script>

<style scoped>
.ghost {
  opacity: 0.5;
  background: #c8ebfb;
}

.column-container {
  min-height: 50px;
  padding: 10px;
}
</style>