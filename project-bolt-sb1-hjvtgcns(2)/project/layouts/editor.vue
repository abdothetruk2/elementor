<template>
  <div  style="width:100%" class="editor-layout flex h-screen overflow-hidden  ">
    <!-- Top Navbar -->
    <div class="editor-navbar h-14 bg-white border-b border-gray-200 w-full fixed top-0 left-0 z-50 flex items-center px-4 justify-between">
      <div class="flex items-center">
        <Logo />
        <div class="ml-4 flex space-x-4">
          <button class="btn btn-primary text-sm" @click="$emit('save')">
            Save
          </button>
          <button class="btn bg-gray-100 hover:bg-gray-200 text-gray-700 text-sm" @click="$emit('preview')">
            Preview
          </button>
        </div>
      </div>
      <div class="flex items-center space-x-4">
        <div class="flex items-center space-x-2 border border-gray-200 rounded-md p-1">
          <button class="p-1.5 rounded hover:bg-gray-100" title="Desktop View">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
            </svg>
          </button>
          <button class="p-1.5 rounded hover:bg-gray-100" title="Tablet View">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 18h.01M7 21h10a2 2 0 002-2V5a2 2 0 00-2-2H7a2 2 0 00-2 2v14a2 2 0 002 2z" />
            </svg>
          </button>
          <button class="p-1.5 rounded hover:bg-gray-100" title="Mobile View">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 18h.01M8 21h8a2 2 0 002-2V5a2 2 0 00-2-2H8a2 2 0 00-2 2v14a2 2 0 002 2z" />
            </svg>
          </button>
        </div>
        <button class="btn bg-gray-100 hover:bg-gray-200 text-gray-700 text-sm">
          Publish
        </button>
        <div class="flex items-center space-x-2">
          <button class="p-1.5 rounded-full hover:bg-gray-100" title="Undo">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
            </svg>
          </button>
          <button class="p-1.5 rounded-full hover:bg-gray-100" title="Redo">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3" />
            </svg>
          </button>
        </div>
      </div>
    </div>
    <slot />
  </div>
</template>

<script setup>
defineEmits(['save', 'preview']);
</script>