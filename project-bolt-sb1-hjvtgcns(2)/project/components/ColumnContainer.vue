<template>
  <div 
    class="column-container relative cursor-pointer"
    @click="handleColumnClick"
    @dragover.prevent
    @drop="handleDrop"
  >
    <div class="column-content min-h-[100px]">
      <draggable 
        v-if="columnElements && columnElements.length >= 0"
        v-model="columnElements"
        group="elements"
        :animation="200"
        ghost-class="ghost"
        handle=".drag-handle"
        @start="dragging = true"
        @end="dragging = false"
        @add="handleAdd"
      >
        <template #item="{ element, index }">
          <ElementContainer 
            :element="element"
            :index="index"
            :key="element.id"
            @select="selectElement"
            @delete="deleteElement"
            @replace="replaceElement"
            @move="moveElement"
            @drop="(event) => handleReplace(event, element)"
          />
        </template>
      </draggable>
      
      <div 
        v-if="columnElements.length === 0" 
        class="empty-column flex items-center justify-center p-4 h-32 border border-dashed border-gray-300 rounded hover:border-primary hover:bg-gray-50 transition-colors duration-200"
        @dragover.prevent
        @drop="handleDrop"
      >
        <p class="text-sm text-gray-400">Click or drag widgets here</p>
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
  previewMode: {
    type: Boolean,
    default: false
  }
});

const emit = defineEmits(['select', 'update', 'delete', 'select-elements']);

const dragging = ref(false);

const columnElements = computed({
  get: () => props.column.elements || [],
  set: (newElements) => {
    emit('update', { ...props.column, elements: newElements });
  }
});

function handleColumnClick() {
  if (props.previewMode) return;
  
  if (columnElements.value.length === 0) {
    emit('select-elements');
  }
}

function handleDrop(event) {
  if (props.previewMode) return;
  
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
    
    const updatedElements = [...columnElements.value, newElement];
    emit('update', { ...props.column, elements: updatedElements });
  } catch (error) {
    console.error('Error processing drop event:', error);
  }
}

function handleReplace(event, targetElement) {
  if (props.previewMode) return;
  
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
        ...targetElement.settings
      }
    };
    
    const elements = [...columnElements.value];
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
  if (props.previewMode) return;
  
  const newElement = event.item?.__draggable_context?.element;
  if (newElement) {
    if (!newElement.id) {
      newElement.id = uuidv4();
    }
    
    if (!newElement.settings) {
      newElement.settings = getDefaultSettings(newElement.type);
    }
    
    const updatedElements = [...columnElements.value];
    emit('update', { ...props.column, elements: updatedElements });
  }
}

function selectElement(element) {
  if (!props.previewMode) {
    emit('select', element);
  }
}

function deleteElement(elementId) {
  if (!props.previewMode) {
    const updatedElements = columnElements.value.filter(el => el.id !== elementId);
    emit('update', { ...props.column, elements: updatedElements });
  }
}

function replaceElement(elementId, newType) {
  if (props.previewMode) return;
  
  const elements = [...columnElements.value];
  const targetIndex = elements.findIndex(el => el.id === elementId);
  
  if (targetIndex !== -1) {
    const existingElement = elements[targetIndex];
    
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
    
    elements[targetIndex] = newElement;
    emit('update', { ...props.column, elements });
  }
}

function moveElement(elementId, direction) {
  if (props.previewMode) return;
  
  const elements = [...columnElements.value];
  const currentIndex = elements.findIndex(el => el.id === elementId);
  
  if (currentIndex === -1) return;
  
  let newIndex;
  if (direction === 'up') {
    newIndex = Math.max(0, currentIndex - 1);
  } else {
    newIndex = Math.min(elements.length - 1, currentIndex + 1);
  }
  
  if (newIndex === currentIndex) return;
  
  [elements[currentIndex], elements[newIndex]] = [elements[newIndex], elements[currentIndex]];
  
  emit('update', { ...props.column, elements });
}

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
.column-container {
  min-height: 50px;
  padding: 10px;
  transition: all 0.2s ease;
}

.empty-column {
  transition: all 0.2s ease;
}

.empty-column.drag-over {
  border-color: var(--color-primary);
  background-color: rgba(93, 59, 238, 0.05);
}

.column-container:hover .empty-column {
  border-color: var(--color-primary-light);
  background-color: rgba(93, 59, 238, 0.02);
}
</style>