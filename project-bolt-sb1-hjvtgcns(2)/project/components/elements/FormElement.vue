<template>
  <div :style="containerStyle">
    <form @submit.prevent="handleSubmit" class="space-y-4">
      <div v-for="field in element.settings.fields" :key="field.id" class="form-field">
        <label :for="field.id" class="block text-sm font-medium text-gray-700 mb-1">
          {{ field.label }}
          <span v-if="field.required" class="text-red-500">*</span>
        </label>
        
        <input 
          v-if="field.type === 'text' || field.type === 'email'"
          :type="field.type"
          :id="field.id"
          v-model="formData[field.id]"
          :required="field.required"
          :placeholder="field.placeholder"
          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-primary"
        />
        
        <textarea
          v-else-if="field.type === 'textarea'"
          :id="field.id"
          v-model="formData[field.id]"
          :required="field.required"
          :placeholder="field.placeholder"
          rows="4"
          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-primary"
        ></textarea>
        
        <select
          v-else-if="field.type === 'select'"
          :id="field.id"
          v-model="formData[field.id]"
          :required="field.required"
          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-primary"
        >
          <option value="">Select an option</option>
          <option v-for="option in field.options" :key="option.value" :value="option.value">
            {{ option.label }}
          </option>
        </select>
      </div>
      
      <div :style="{ textAlign: element.settings.submitButton?.align || 'left' }">
        <button 
          type="submit"
          class="btn btn-primary"
        >
          {{ element.settings.submitButton?.text || 'Submit' }}
        </button>
      </div>
    </form>
    
    <div v-if="showSuccess" class="mt-4 p-4 bg-green-100 text-green-700 rounded-md">
      {{ element.settings.successMessage }}
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

const formData = ref({});
const showSuccess = ref(false);

const containerStyle = computed(() => {
  const { settings } = props.element;
  const { margin, padding, width, height, widthUnit, heightUnit } = settings;
  
  return {
    margin: `${margin?.top || '0px'} ${margin?.right || '0px'} ${margin?.bottom || '0px'} ${margin?.left || '0px'}`,
    padding: `${padding?.top || '0px'} ${padding?.right || '0px'} ${padding?.bottom || '0px'} ${padding?.left || '0px'}`,
    width: width ? `${width}${widthUnit || '%'}` : '100%',
    height: height ? `${height}${heightUnit || 'px'}` : 'auto'
  };
});

function handleSubmit() {
  // Here you would typically send the form data to a server
  console.log('Form submitted:', formData.value);
  showSuccess.value = true;
  formData.value = {};
  
  setTimeout(() => {
    showSuccess.value = false;
  }, 3000);
}
</script>