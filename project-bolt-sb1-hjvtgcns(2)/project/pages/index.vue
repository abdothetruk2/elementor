<template>
  <div class="flex flex-col items-center justify-center min-h-screen bg-gray-50 p-8">
    <Logo class="mb-8" />
    <h1 class="text-4xl font-bold text-gray-900 mb-6">Welcome to ElementorClone</h1>
    <p class="text-lg text-gray-600 max-w-md text-center mb-8">
      Build beautiful websites with our intuitive drag and drop page builder. No coding required.
    </p>
    <div class="flex space-x-4">
      <NuxtLink to="/editor" class="btn btn-primary">
        Start Building
      </NuxtLink>
      <button class="btn bg-white border border-gray-300 text-gray-700 hover:bg-gray-50">
        Learn More
      </button>
    </div>
    
    <div class="mt-16 w-full max-w-4xl">
      <h2 class="text-2xl font-semibold text-center text-gray-800 mb-8">Features</h2>
      <div class="grid md:grid-cols-3 gap-8">
        <div class="bg-white p-6 rounded-lg shadow-sm">
          <div class="w-12 h-12 bg-purple-100 rounded-md flex items-center justify-center text-purple-600 mb-4">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2V6zM14 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2V6zM4 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2v-2zM14 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2v-2z" />
            </svg>
          </div>
          <h3 class="text-lg font-medium text-gray-800 mb-2">Drag & Drop Editor</h3>
          <p class="text-gray-600">Build your pages visually with our intuitive drag and drop interface.</p>
        </div>
        
        <div class="bg-white p-6 rounded-lg shadow-sm">
          <div class="w-12 h-12 bg-blue-100 rounded-md flex items-center justify-center text-blue-600 mb-4">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
            </svg>
          </div>
          <h3 class="text-lg font-medium text-gray-800 mb-2">Responsive Design</h3>
          <p class="text-gray-600">Create websites that look great on any device with device-specific controls.</p>
        </div>
        
        <div class="bg-white p-6 rounded-lg shadow-sm">
          <div class="w-12 h-12 bg-green-100 rounded-md flex items-center justify-center text-green-600 mb-4">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
            </svg>
          </div>
          <h3 class="text-lg font-medium text-gray-800 mb-2">Styling Controls</h3>
          <p class="text-gray-600">Customize every aspect of your elements with powerful styling controls.</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
// Welcome page
</script>