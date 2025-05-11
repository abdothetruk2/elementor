<template>
  <div class="element-settings">
    <template v-if="element">
      <div class="mb-4 pb-4 border-b border-gray-700">
        <h3 class="text-sm font-semibold text-gray-300 mb-4">{{ capitalize(element.type) }} Settings</h3>
        
        <!-- Content settings based on element type -->
        <div v-if="element.type === 'heading'" class="space-y-4">
          <div>
            <label class="text-xs text-gray-400 block mb-1">Heading Text</label>
            <input 
              type="text" 
              v-model="elementData.content" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            />
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Tag</label>
            <select 
              v-model="elementData.settings.tag" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option v-for="tag in ['h1', 'h2', 'h3', 'h4', 'h5', 'h6']" :key="tag" :value="tag">{{ tag }}</option>
            </select>
          </div>
        </div>
        
        <div v-else-if="element.type === 'text'" class="space-y-4">
          <div>
            <label class="text-xs text-gray-400 block mb-1">Text Content</label>
            <textarea 
              v-model="elementData.content" 
              rows="4"
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            ></textarea>
          </div>
        </div>
        
        <div v-else-if="element.type === 'button'" class="space-y-4">
          <div>
            <label class="text-xs text-gray-400 block mb-1">Button Text</label>
            <input 
              type="text" 
              v-model="elementData.content.text" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            />
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">URL</label>
            <input 
              type="text" 
              v-model="elementData.content.url" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            />
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Open in new tab</label>
            <select 
              v-model="elementData.content.target" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option value="_self">No</option>
              <option value="_blank">Yes</option>
            </select>
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Size</label>
            <select 
              v-model="elementData.settings.size" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option v-for="size in ['small', 'medium', 'large']" :key="size" :value="size">{{ capitalize(size) }}</option>
            </select>
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Type</label>
            <select 
              v-model="elementData.settings.type" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option v-for="type in ['primary', 'secondary', 'outline', 'link']" :key="type" :value="type">{{ capitalize(type) }}</option>
            </select>
          </div>
        </div>
        
        <div v-else-if="element.type === 'image'" class="space-y-4">
          <div>
            <label class="text-xs text-gray-400 block mb-1">Image URL</label>
            <input 
              type="text" 
              v-model="elementData.content.url" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            />
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Alt Text</label>
            <input 
              type="text" 
              v-model="elementData.content.alt" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            />
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Border Radius</label>
            <input 
              type="text" 
              v-model="elementData.settings.borderRadius" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            />
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Object Fit</label>
            <select 
              v-model="elementData.settings.objectFit" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option v-for="fit in ['cover', 'contain', 'fill', 'none']" :key="fit" :value="fit">{{ capitalize(fit) }}</option>
            </select>
          </div>
        </div>
        
        <div v-else-if="element.type === 'divider'" class="space-y-4">
          <div>
            <label class="text-xs text-gray-400 block mb-1">Style</label>
            <select 
              v-model="elementData.settings.style" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option v-for="style in ['solid', 'dashed', 'dotted', 'double']" :key="style" :value="style">{{ capitalize(style) }}</option>
            </select>
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Color</label>
            <div class="flex">
              <span class="w-6 h-6 rounded-l" :style="{ backgroundColor: elementData.settings.color }"></span>
              <input 
                type="text" 
                v-model="elementData.settings.color" 
                class="w-full bg-gray-700 border border-gray-600 rounded-r px-3 py-1 text-sm text-white"
              />
            </div>
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Width</label>
            <input 
              type="text" 
              v-model="elementData.settings.width" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            />
          </div>
        </div>
        
        <div v-else-if="element.type === 'spacer'" class="space-y-4">
          <div>
            <label class="text-xs text-gray-400 block mb-1">Height</label>
            <input 
              type="text" 
              v-model="elementData.settings.height" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            />
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Visible on Mobile</label>
            <select 
              v-model="elementData.settings.mobileVisible" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option :value="true">Yes</option>
              <option :value="false">No</option>
            </select>
          </div>
        </div>

        <div v-else-if="element.type === 'code'" class="space-y-4">
          <div>
            <label class="text-xs text-gray-400 block mb-1">Code Type</label>
            <select 
              v-model="elementData.settings.type" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option value="css">CSS</option>
              <option value="javascript">JavaScript</option>
              <option value="html">HTML</option>
            </select>
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Code Content</label>
            <textarea 
              v-model="elementData.content" 
              rows="8"
              :placeholder="getCodePlaceholder(elementData.settings.type)"
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white font-mono"
            ></textarea>
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Theme</label>
            <select 
              v-model="elementData.settings.theme" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option value="dark">Dark</option>
              <option value="light">Light</option>
            </select>
          </div>
          
          <div class="flex items-center space-x-4">
            <label class="flex items-center">
              <input 
                type="checkbox" 
                v-model="elementData.settings.lineNumbers"
                class="bg-gray-700 border-gray-600 rounded"
              >
              <span class="ml-2 text-xs text-gray-400">Show Line Numbers</span>
            </label>
            
            <label class="flex items-center">
              <input 
                type="checkbox" 
                v-model="elementData.settings.readOnly"
                class="bg-gray-700 border-gray-600 rounded"
              >
              <span class="ml-2 text-xs text-gray-400">Read Only</span>
            </label>
          </div>
        </div>
      </div>
      
      <!-- Common style settings for all elements -->
      <div class="mb-4">
        <h3 class="text-sm font-semibold text-gray-300 mb-4">Style</h3>
        
        <!-- Typography - Only show for text-based elements -->
        <div v-if="['heading', 'text', 'button'].includes(element.type)" class="mb-4">
          <h4 class="panel-title text-gray-400">Typography</h4>
          
          <div class="grid grid-cols-2 gap-2 mb-2">
            <div>
              <label class="text-xs text-gray-400 block mb-1">Size</label>
              <div class="flex">
                <input 
                  type="text" 
                  v-model="elementData.settings.typography.size" 
                  class="w-full bg-gray-700 border border-gray-600 rounded-l px-3 py-2 text-sm text-white"
                />
                <span class="bg-gray-600 px-2 flex items-center rounded-r text-xs text-gray-300">px</span>
              </div>
            </div>
            
            <div>
              <label class="text-xs text-gray-400 block mb-1">Weight</label>
              <select 
                v-model="elementData.settings.typography.weight" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
              >
                <option v-for="weight in ['300', '400', '500', '600', '700']" :key="weight" :value="weight">{{ weight }}</option>
              </select>
            </div>
          </div>
          
          <div class="mb-2">
            <label class="text-xs text-gray-400 block mb-1">Line Height</label>
            <input 
              type="text" 
              v-model="elementData.settings.typography.lineHeight" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            />
          </div>
          
          <div class="mb-2">
            <label class="text-xs text-gray-400 block mb-1">Text Alignment</label>
            <select 
              v-model="elementData.settings.align" 
              class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
            >
              <option v-for="align in ['left', 'center', 'right', 'justify']" :key="align" :value="align">{{ capitalize(align) }}</option>
            </select>
          </div>
        </div>
        
        <!-- Background for applicable elements -->
        <div v-if="['heading', 'text', 'button', 'image'].includes(element.type)" class="mb-4">
          <h4 class="panel-title text-gray-400">Background</h4>
          <div class="flex">
            <span class="w-6 h-6 rounded-l" :style="{ backgroundColor: elementData.settings.backgroundColor || 'transparent' }"></span>
            <input 
              type="text" 
              v-model="elementData.settings.backgroundColor" 
              placeholder="transparent"
              class="w-full bg-gray-700 border border-gray-600 rounded-r px-3 py-1 text-sm text-white"
            />
          </div>
        </div>
        
        <!-- Spacing -->
        <div class="mb-4">
          <h4 class="panel-title text-gray-400">Margin</h4>
          
          <div class="grid grid-cols-4 gap-1 mb-2">
            <div>
              <label class="text-xs text-gray-400 block mb-1">Top</label>
              <input 
                type="text" 
                v-model="elementData.settings.margin.top" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-2 py-1 text-sm text-white"
              />
            </div>
            <div>
              <label class="text-xs text-gray-400 block mb-1">Right</label>
              <input 
                type="text" 
                v-model="elementData.settings.margin.right" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-2 py-1 text-sm text-white"
              />
            </div>
            <div>
              <label class="text-xs text-gray-400 block mb-1">Bottom</label>
              <input 
                type="text" 
                v-model="elementData.settings.margin.bottom" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-2 py-1 text-sm text-white"
              />
            </div>
            <div>
              <label class="text-xs text-gray-400 block mb-1">Left</label>
              <input 
                type="text" 
                v-model="elementData.settings.margin.left" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-2 py-1 text-sm text-white"
              />
            </div>
          </div>

          <h4 class="panel-title text-gray-400 mt-4">Padding</h4>
          
          <div class="grid grid-cols-4 gap-1 mb-2">
            <div>
              <label class="text-xs text-gray-400 block mb-1">Top</label>
              <input 
                type="text" 
                v-model="elementData.settings.padding.top" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-2 py-1 text-sm text-white"
              />
            </div>
            <div>
              <label class="text-xs text-gray-400 block mb-1">Right</label>
              <input 
                type="text" 
                v-model="elementData.settings.padding.right" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-2 py-1 text-sm text-white"
              />
            </div>
            <div>
              <label class="text-xs text-gray-400 block mb-1">Bottom</label>
              <input 
                type="text" 
                v-model="elementData.settings.padding.bottom" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-2 py-1 text-sm text-white"
              />
            </div>
            <div>
              <label class="text-xs text-gray-400 block mb-1">Left</label>
              <input 
                type="text" 
                v-model="elementData.settings.padding.left" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-2 py-1 text-sm text-white"
              />
            </div>
          </div>
        </div>

        <div class="mb-4">
          <h4 class="panel-title text-gray-400">Size</h4>
          
          <div class="grid grid-cols-2 gap-2 mb-2">
            <div>
              <label class="text-xs text-gray-400 block mb-1">Width</label>
              <div class="flex">
                <input 
                  type="text" 
                  v-model="elementData.settings.width" 
                  class="w-full bg-gray-700 border border-gray-600 rounded-l px-2 py-1 text-sm text-white"
                />
                <select 
                  v-model="elementData.settings.widthUnit" 
                  class="bg-gray-600 px-2 text-xs text-white rounded-r"
                >
                  <option value="%">%</option>
                  <option value="px">px</option>
                  <option value="auto">auto</option>
                </select>
              </div>
            </div>
            
            <div>
              <label class="text-xs text-gray-400 block mb-1">Height</label>
              <div class="flex">
                <input 
                  type="text" 
                  v-model="elementData.settings.height" 
                  class="w-full bg-gray-700 border border-gray-600 rounded-l px-2 py-1 text-sm text-white"
                />
                <select 
                  v-model="elementData.settings.heightUnit" 
                  class="bg-gray-600 px-2 text-xs text-white rounded-r"
                >
                  <option value="px">px</option>
                  <option value="%">%</option>
                  <option value="auto">auto</option>
                </select>
              </div>
            </div>
          </div>
        </div>
        
        <!-- Colors -->
        <div class="mb-4">
          <h4 class="panel-title text-gray-400">Colors</h4>
          
          <div class="grid grid-cols-2 gap-2">
            <div>
              <label class="text-xs text-gray-400 block mb-1">Text</label>
              <div class="flex">
                <span class="w-6 h-6 rounded-l" :style="{ backgroundColor: elementData.settings.color }"></span>
                <input 
                  type="text" 
                  v-model="elementData.settings.color" 
                  class="w-full bg-gray-700 border border-gray-600 rounded-r px-3 py-1 text-sm text-white"
                />
              </div>
            </div>
          </div>
        </div>

        <!-- Border settings -->
        <div v-if="['heading', 'text', 'button', 'image'].includes(element.type)" class="mb-4">
          <h4 class="panel-title text-gray-400">Border</h4>
          
          <div class="grid grid-cols-2 gap-2 mb-2">
            <div>
              <label class="text-xs text-gray-400 block mb-1">Style</label>
              <select 
                v-model="elementData.settings.border.style" 
                class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
              >
                <option v-for="style in ['none', 'solid', 'dashed', 'dotted']" :key="style" :value="style">{{ capitalize(style) }}</option>
              </select>
            </div>
            
            <div>
              <label class="text-xs text-gray-400 block mb-1">Width</label>
              <div class="flex">
                <input 
                  type="number" 
                  v-model="elementData.settings.border.width" 
                  class="w-full bg-gray-700 border border-gray-600 rounded-l px-3 py-2 text-sm text-white"
                />
                <span class="bg-gray-600 px-2 flex items-center rounded-r text-xs text-gray-300">px</span>
              </div>
            </div>
          </div>
          
          <div>
            <label class="text-xs text-gray-400 block mb-1">Color</label>
            <div class="flex">
              <span class="w-6 h-6 rounded-l" :style="{ backgroundColor: elementData.settings.border?.color || '#cccccc' }"></span>
              <input 
                type="text" 
                v-model="elementData.settings.border.color" 
                class="w-full bg-gray-700 border border-gray-600 rounded-r px-3 py-1 text-sm text-white"
              />
            </div>
          </div>
        </div>
      </div>
      
      <!-- Advanced settings -->
      <div>
        <h3 class="text-sm font-semibold text-gray-300 mb-4">Advanced</h3>
        
        <div class="mb-4">
          <label class="text-xs text-gray-400 block mb-1">CSS Class</label>
          <input 
            type="text" 
            v-model="elementData.settings.cssClass" 
            placeholder="custom-class"
            class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white"
          />
        </div>
        
        <div class="mb-4">
          <label class="text-xs text-gray-400 block mb-1">Element ID</label>
          <input 
            type="text" 
            disabled
            :value="element.id"
            class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white opacity-60"
          />
        </div>
      </div>

      <div class="mb-4">
        <h4 class="panel-title text-gray-400">Custom Code</h4>
        
        <div class="mb-2">
          <label class="text-xs text-gray-400 block mb-1">Custom CSS</label>
          <textarea 
            v-model="elementData.settings.customCSS" 
            rows="4"
            placeholder=".element { /* your styles */ }"
            class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white font-mono"
          ></textarea>
        </div>
        
        <div>
          <label class="text-xs text-gray-400 block mb-1">Custom JavaScript</label>
          <textarea 
            v-model="elementData.settings.customJS" 
            rows="4"
            placeholder="// Your JavaScript code"
            class="w-full bg-gray-700 border border-gray-600 rounded px-3 py-2 text-sm text-white font-mono"
          ></textarea>
        </div>
      </div>
    </template>
    
    <div v-else class="text-center text-gray-400 py-8">
      <p>Select an element to edit</p>
    </div>
  </div>
</template>


<script setup>
import { ref, watch } from 'vue';
import { v4 as uuidv4 } from 'uuid';

const props = defineProps({
  element: {
    type: Object,
    default: null
  }
});

const emit = defineEmits(['update']);

const elementData = ref(null);

// Add capitalize function
const capitalize = (str) => {
  if (!str) return '';
  return str.charAt(0).toUpperCase() + str.slice(1);
};

// Initialize element data
watch(() => props.element, (newElement) => {
  if (newElement) {
    elementData.value = initializeElementData(newElement);
  } else {
    elementData.value = null;
  }
}, { immediate: true });

// Watch for changes
watch(elementData, (newData) => {
  if (newData) {
    emit('update', newData);
  }
}, { deep: true });

// Navbar methods
function addMenuItem() {
  if (!elementData.value.content.menuItems) {
    elementData.value.content.menuItems = [];
  }
  
  elementData.value.content.menuItems.push({
    id: uuidv4(),
    text: 'New Item',
    url: '#'
  });
}

function removeMenuItem(index) {
  elementData.value.content.menuItems.splice(index, 1);
}

// Form methods
function addFormField() {
  if (!elementData.value.settings.fields) {
    elementData.value.settings.fields = [];
  }
  
  elementData.value.settings.fields.push({
    id: uuidv4(),
    type: 'text',
    label: 'New Field',
    required: false,
    placeholder: ''
  });
}

function removeFormField(index) {
  elementData.value.settings.fields.splice(index, 1);
}

// Accordion methods
function addAccordionItem() {
  if (!elementData.value.content.items) {
    elementData.value.content.items = [];
  }
  
  elementData.value.content.items.push({
    id: uuidv4(),
    title: 'New Item',
    content: 'Add your content here'
  });
}

function removeAccordionItem(index) {
  elementData.value.content.items.splice(index, 1);
}

// Helper function to initialize element data
function initializeElementData(element) {
  const data = JSON.parse(JSON.stringify(element));
  
  // Initialize settings based on element type
  if (!data.settings) {
    data.settings = {};
  }
  
  switch (element.type) {
    case 'navbar':
      if (!data.settings.logoHeight) data.settings.logoHeight = 40;
      if (!data.settings.sticky) data.settings.sticky = false;
      if (!data.content.menuItems) {
        data.content.menuItems = [
          { id: uuidv4(), text: 'Home', url: '#' },
          { id: uuidv4(), text: 'About', url: '#' }
        ];
      }
      break;
      
    case 'form':
      if (!data.settings.fields) {
        data.settings.fields = [
          {
            id: uuidv4(),
            type: 'text',
            label: 'Name',
            required: true,
            placeholder: 'Enter your name'
          },
          {
            id: uuidv4(),
            type: 'email',
            label: 'Email',
            required: true,
            placeholder: 'Enter your email'
          }
        ];
      }
      if (!data.settings.submitButton) {
        data.settings.submitButton = {
          text: 'Submit',
          align: 'left'
        };
      }
      if (!data.settings.successMessage) {
        data.settings.successMessage = 'Thank you for your submission!';
      }
      break;
      
    case 'code':
      if (!data.settings.language) data.settings.language = 'javascript';
      if (!data.settings.showLineNumbers) data.settings.showLineNumbers = true;
      if (!data.settings.copyButton) data.settings.copyButton = true;
      if (!data.content) {
        data.content = '// Add your code here\nconsole.log("Hello World!");';
      }
      break;
      
    case 'accordion':
      if (!data.settings.allowMultiple) data.settings.allowMultiple = false;
      if (!data.content.items) {
        data.content.items = [
          {
            id: uuidv4(),
            title: 'Accordion Item 1',
            content: 'Content for accordion item 1'
          },
          {
            id: uuidv4(),
            title: 'Accordion Item 2',
            content: 'Content for accordion item 2'
          }
        ];
      }
      break;
  }
  
  return data;
}
</script>