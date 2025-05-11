<template>
  <div 
    :class="['section-container relative border-2', selected && !previewMode ? 'border-[var(--color-primary)]' : 'border-transparent hover:border-[var(--color-primary-light)] border-dashed']"
    @click.stop="selectSection"
  >
    <div class="section-content" :style="sectionStyle">
      <div class="columns-container flex flex-wrap" :style="{ margin: `0 -${columnGap/2}px` }">
        <ColumnContainer 
          v-for="(column, colIndex) in section.columns" 
          :key="column.id"
          :column="column"
          :index="colIndex"
          :style="{ width: column.width, padding: `0 ${columnGap/2}px` }"
          @select="selectElement"
          @update="updateColumn"
          @delete="deleteElement"
          :preview-mode="previewMode"
        />
      </div>
      
      <!-- Section Controls -->
      <div 
        v-if="selected && !previewMode" 
        class="section-controls absolute top-0 left-0 transform -translate-y-full bg-[var(--color-primary)] text-white flex items-center text-xs rounded-t-md overflow-hidden"
      >
        <div class="p-1 px-2 font-medium">Section</div>
        <div class="flex">
          <button 
            class="p-1 hover:bg-[var(--color-primary-dark)]" 
            title="Add Column"
            @click.stop="addColumn"
          >
            Add Column
          </button>
          <button 
            class="p-1 hover:bg-[var(--color-primary-dark)]" 
            title="Delete Section"
            @click.stop="deleteSection"
          >
            Delete
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { v4 as uuidv4 } from 'uuid';

const props = defineProps({
  section: {
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

const emit = defineEmits(['select', 'update', 'delete']);

const selected = ref(false);
const columnGap = 20; // Gap between columns in pixels

// Computed styles for the section
const sectionStyle = computed(() => {
  const { padding, margin, background } = props.section.settings;
  
  return {
    padding: `${padding.top} ${padding.right} ${padding.bottom} ${padding.left}`,
    margin: `${margin.top} ${margin.right} ${margin.bottom} ${margin.left}`,
    backgroundColor: background.color,
    backgroundImage: background.image ? `url(${background.image})` : 'none',
    backgroundPosition: background.position,
    backgroundRepeat: background.repeat,
    backgroundSize: background.size
  };
});

// Select this section
function selectSection() {
  if (!props.previewMode) {
    selected.value = true;
    emit('select', props.section);
  }
}

// Add a new column to the section
function addColumn() {
  const newColumn = {
    id: uuidv4(),
    width: calculateColumnWidth(props.section.columns.length + 1),
    elements: []
  };
  
  // Recalculate all column widths
  const updatedColumns = [...props.section.columns.map(col => ({ ...col })), newColumn];
  updatedColumns.forEach((col, i) => {
    col.width = calculateColumnWidth(updatedColumns.length, i);
  });
  
  const updatedSection = {
    ...props.section,
    columns: updatedColumns
  };
  
  emit('update', props.section.id, updatedSection);
}

// Update a column
function updateColumn(updatedColumn) {
  const updatedColumns = [...props.section.columns];
  const columnIndex = updatedColumns.findIndex(c => c.id === updatedColumn.id);
  
  if (columnIndex !== -1) {
    updatedColumns[columnIndex] = updatedColumn;
    
    const updatedSection = {
      ...props.section,
      columns: updatedColumns
    };
    
    emit('update', props.section.id, updatedSection);
  }
}

// Delete section
function deleteSection() {
  emit('delete', props.section.id);
}

// Delete element
function deleteElement(elementId, columnId) {
  emit('delete', elementId, props.section.id, columnId);
}

// Calculate column width based on number of columns
function calculateColumnWidth(totalColumns, columnIndex) {
  // Default to equal widths
  return `${100 / totalColumns}%`;
}

// Select an element within a column
function selectElement(element) {
  if (!props.previewMode) {
    // Deselect this section
    selected.value = false;
    // Forward the element selection to the parent
    emit('select', element);
  }
}
</script>

<style scoped>
.section-container {
  position: relative;
  transition: all 0.2s ease;
  margin: 20px 0;
}

.section-controls {
  z-index: 10;
}
</style>