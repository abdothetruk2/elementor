<template>
  <NuxtLayout name="editor" @save="saveContent" @preview="openPreview">
    <div class="editor-container flex h-screen pt-14 w-full">
      <!-- Left Sidebar - Elements Panel -->
      <div class="editor-sidebar w-64 bg-gray-800 text-white h-full overflow-y-auto flex flex-col">
        <div class="p-4 border-b border-gray-700">
          <div class="flex items-center space-x-2">
            <button 
              v-for="tab in ['Elements', 'Settings', 'History']" 
              :key="tab"
              @click="activeTab = tab"
              :class="[
                'px-4 py-2 text-sm rounded transition-colors', 
                activeTab === tab ? 'bg-gray-700 text-white' : 'text-gray-400 hover:text-white'
              ]"
            >
              {{ tab }}
            </button>
          </div>
        </div>
        
        <div v-if="activeTab === 'Elements'" class="p-4 flex-1 overflow-y-auto">
          <div class="mb-6">
            <h3 class="text-xs uppercase text-gray-400 font-medium mb-3">Basic Elements</h3>
            <div class="grid grid-cols-2 gap-2">
              <WidgetItem 
                v-for="widget in basicWidgets" 
                :key="widget.type" 
                :widget="widget" 
                @select="handleWidgetSelect"
              />
            </div>
          </div>
          
          <div class="mb-6">
            <h3 class="text-xs uppercase text-gray-400 font-medium mb-3">Advanced Elements</h3>
            <div class="grid grid-cols-2 gap-2">
              <WidgetItem 
                v-for="widget in advancedWidgets" 
                :key="widget.type" 
                :widget="widget" 
                @select="handleWidgetSelect"
              />
            </div>
          </div>
        </div>
        
        <div v-else-if="activeTab === 'Settings'" class="p-4 flex-1 overflow-y-auto">
          <ElementSettings 
            v-if="selectedElement" 
            :element="selectedElement" 
            @update="updateElementSettings" 
          />
        </div>

        <div v-else-if="activeTab === 'History'" class="p-4 flex-1 overflow-y-auto">
          <div class="space-y-2">
            <button 
              class="w-full px-4 py-2 bg-gray-700 text-white rounded disabled:opacity-50"
              @click="undo"
              :disabled="!canUndo"
            >
              Undo
            </button>
            <button 
              class="w-full px-4 py-2 bg-gray-700 text-white rounded disabled:opacity-50"
              @click="redo"
              :disabled="!canRedo"
            >
              Redo
            </button>
          </div>
        </div>
      </div>
      
      <!-- Main Content Area -->
      <div class="editor-content flex-1 bg-gray-100 overflow-y-auto">
        <div class="editor-canvas bg-white min-h-screen mx-auto" :style="canvasStyle">
          <draggable 
            v-model="pageContent"
            group="sections"
            :animation="200"
            ghost-class="ghost"
            class="min-h-screen"
            :item-key="'id'"
          >
            <template #item="{ element: section }">
              <SectionContainer 
                :section="section"
                :index="section.index"
                @select="selectElement"
                @update="updateSection"
                @delete="deleteSection"
              />
            </template>
          </draggable>

          <div 
            v-if="pageContent.length === 0" 
            class="empty-canvas flex flex-col items-center justify-center h-96 border-2 border-dashed border-gray-300 rounded-lg m-4"
          >
            <p class="text-gray-500 mb-2">Your canvas is empty</p>
            <button 
              @click="addNewSection" 
              class="mt-4 btn btn-primary text-sm"
            >
              Add Section
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Preview Modal -->
    <Transition name="fade">
      <div v-if="showPreview" class="fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center">
        <div class="bg-white rounded-lg shadow-xl w-full h-full max-w-6xl max-h-[90vh] flex flex-col">
          <div class="p-4 border-b border-gray-200 flex justify-between items-center">
            <h2 class="text-lg font-semibold">Preview</h2>
            <button @click="closePreview" class="text-gray-500 hover:text-gray-700">×</button>
          </div>
          <div class="flex-1 overflow-auto p-4">
            <div class="bg-white mx-auto" :style="canvasStyle">
              <template v-if="pageContent.length > 0">
                <SectionContainer 
                  v-for="section in pageContent" 
                  :key="section.id" 
                  :section="section"
                  :index="0"
                  preview-mode
                />
              </template>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  </NuxtLayout>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';
import { v4 as uuidv4 } from 'uuid';
import draggable from 'vuedraggable';

// State
const activeTab = ref('Elements');
const showPreview = ref(false);
const selectedElement = ref(null);
const pageContent = ref([]);
const history = ref([]);
const historyIndex = ref(-1);

// History management
const canUndo = computed(() => historyIndex.value > 0);
const canRedo = computed(() => historyIndex.value < history.value.length - 1);

function saveToHistory() {
  // Remove any future states if we're not at the end
  if (historyIndex.value < history.value.length - 1) {
    history.value = history.value.slice(0, historyIndex.value + 1);
  }
  
  history.value.push(JSON.stringify(pageContent.value));
  historyIndex.value = history.value.length - 1;
}

function undo() {
  if (canUndo.value) {
    historyIndex.value--;
    pageContent.value = JSON.parse(history.value[historyIndex.value]);
  }
}

function redo() {
  if (canRedo.value) {
    historyIndex.value++;
    pageContent.value = JSON.parse(history.value[historyIndex.value]);
  }
}

// Canvas size based on selected device
const currentDevice = ref('desktop');
const canvasStyle = computed(() => {
  const sizes = {
    desktop: '100%',
    tablet: '768px',
    mobile: '375px'
  };
  
  return {
    width: sizes[currentDevice.value],
    margin: currentDevice.value !== 'desktop' ? '0 auto' : ''
  };
});

// Basic widgets
const basicWidgets = [
  { type: 'heading', label: 'Heading', icon: 'text-size' },
  { type: 'text', label: 'Text', icon: 'text' },
  { type: 'image', label: 'Image', icon: 'photograph' },
  { type: 'button', label: 'Button', icon: 'cursor-click' },
  { type: 'spacer', label: 'Spacer', icon: 'arrows-expand' },
  { type: 'divider', label: 'Divider', icon: 'minus' },
  { 
    type: 'code', 
    label: 'Code', 
    icon: 'code',
    settings: {
      type: 'css', // css, js, or html
      theme: 'dark',
      lineNumbers: true,
      readOnly: false
    }
  }
];

// Advanced widgets
const advancedWidgets = [
  { 
    type: 'video', 
    label: 'Video', 
    icon: 'video-camera',
    settings: {
      url: '',
      autoplay: false,
      controls: true,
      muted: false,
      loop: false
    }
  },
  { 
    type: 'form', 
    label: 'Form', 
    icon: 'document-text',
    settings: {
      fields: [],
      submitButton: { text: 'Submit', align: 'left' },
      successMessage: 'Form submitted successfully!'
    }
  },
  { 
    type: 'gallery', 
    label: 'Gallery', 
    icon: 'photograph',
    settings: {
      images: [],
      columns: 3,
      gap: 20,
      lightbox: true
    }
  },
  { 
    type: 'social', 
    label: 'Social', 
    icon: 'share',
    settings: {
      networks: [
        { type: 'facebook', url: '', enabled: true },
        { type: 'twitter', url: '', enabled: true },
        { type: 'instagram', url: '', enabled: true },
        { type: 'linkedin', url: '', enabled: true }
      ],
      iconSize: '24px',
      iconSpacing: '10px',
      align: 'left'
    }
  }
];

// Handle widget selection
function handleWidgetSelect(widget) {
  const newWidget = {
    id: uuidv4(),
    type: widget.type,
    settings: getDefaultSettings(widget.type),
    content: getDefaultContent(widget.type)
  };
  
  if (pageContent.value.length > 0) {
    const firstSection = pageContent.value[0];
    if (firstSection.columns.length > 0) {
      firstSection.columns[0].elements.push(newWidget);
      saveToHistory();
    }
  } else {
    addNewSection(newWidget);
  }
}

// Add a new section
function addNewSection(initialElement = null) {
  const elements = initialElement ? [initialElement] : [];
  
  const newSection = {
    id: uuidv4(),
    type: 'section',
    settings: {
      padding: { top: '20px', right: '20px', bottom: '20px', left: '20px' },
      margin: { top: '0px', right: '0px', bottom: '0px', left: '0px' },
      background: { color: '#ffffff', image: '', position: 'center center', repeat: 'no-repeat', size: 'cover' }
    },
    columns: [
      {
        id: uuidv4(),
        width: '100%',
        elements: elements
      }
    ]
  };
  
  pageContent.value.push(newSection);
  saveToHistory();
}

// Delete section
function deleteSection(sectionId) {
  const index = pageContent.value.findIndex(s => s.id === sectionId);
  if (index !== -1) {
    pageContent.value.splice(index, 1);
    saveToHistory();
  }
}

// Delete element
function deleteElement(elementId, sectionId, columnId) {
  const section = pageContent.value.find(s => s.id === sectionId);
  if (section) {
    const column = section.columns.find(c => c.id === columnId);
    if (column) {
      const elementIndex = column.elements.findIndex(e => e.id === elementId);
      if (elementIndex !== -1) {
        column.elements.splice(elementIndex, 1);
        
        // If this was the selected element, clear the selection
        if (selectedElement.value && selectedElement.value.id === elementId) {
          selectedElement.value = null;
        }
        
        saveToHistory();
      }
    }
  }
}

// Update section
function updateSection(sectionId, updatedSection) {
  const index = pageContent.value.findIndex(s => s.id === sectionId);
  if (index !== -1) {
    pageContent.value[index] = updatedSection;
    saveToHistory();
  }
}

// Update element settings
function updateElementSettings(updatedElement) {
  // Find and update the element in the page content
  pageContent.value.forEach(section => {
    section.columns.forEach(column => {
      const elementIndex = column.elements.findIndex(e => e.id === updatedElement.id);
      if (elementIndex !== -1) {
        column.elements[elementIndex] = updatedElement;
        saveToHistory();
      }
    });
  });
}

// Select element
function selectElement(element) {
  selectedElement.value = element;
  
  // Switch to Settings tab when selecting an element
  if (element && activeTab.value === 'Elements') {
    activeTab.value = 'Settings';
  }
}

// Preview functions
function openPreview() {
  showPreview.value = true;
}

function closePreview() {
  showPreview.value = false;
}

// Save function
function saveContent() {
  try {
    localStorage.setItem('elementorCloneContent', JSON.stringify(pageContent.value));
    alert('Content saved successfully!');
  } catch (error) {
    console.error('Error saving content:', error);
    alert('Error saving content. Please try again.');
  }
}

// Get default settings for a widget type
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

// Get default content for a widget type
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

// Handle keyboard shortcuts
function handleKeyDown(event) {
  if (event.key === 'Delete' && selectedElement.value) {
    // Find and delete the selected element
    pageContent.value.forEach(section => {
      section.columns.forEach(column => {
        const elementIndex = column.elements.findIndex(e => e.id === selectedElement.value.id);
        if (elementIndex !== -1) {
          column.elements.splice(elementIndex, 1);
          selectedElement.value = null;
          saveToHistory();
        }
      });
    });
  }
}

// Initialize
onMounted(() => {
  const savedContent = localStorage.getItem('elementorCloneContent');
  if (savedContent) {
    try {
      pageContent.value = JSON.parse(savedContent);
      saveToHistory(); // Initialize history with loaded content
    } catch (error) {
      console.error('Error loading saved content:', error);
    }
  }
  
  // Add delete key handler
  window.addEventListener('keydown', handleKeyDown);
});

// Cleanup
onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown);
});
</script>

<style>
.ghost {
  opacity: 0.5;
  background: #c8ebfb;
}
</style>