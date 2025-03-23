<template>
    <div>
      <!-- Overlay -->
      <div
        v-if="isOpen"
        class="fixed inset-0 bg-black bg-opacity-50 z-40"
        @click="toggleSidebar"
      ></div>
  
      <!-- Sidebar -->
      <div
        :class="[
          'fixed inset-y-0 left-0 transform bg-white w-64 transition-transform z-50',
          isOpen ? 'translate-x-0' : '-translate-x-full',
        ]"
      >
        <div class="p-4">
          <button @click="toggleSidebar" class="text-gray-700">
            <!-- Close Icon -->
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-6 w-6"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M6 18L18 6M6 6l12 12"
              />
            </svg>
          </button>
        </div>
        <nav class="mt-5">
          <a
            href="#"
            class="block px-4 py-2 text-gray-700 hover:bg-gray-200"
            >Home</a
          >
          <a
            href="#"
            class="block px-4 py-2 text-gray-700 hover:bg-gray-200"
            >About</a
          >
          <a
            href="#"
            class="block px-4 py-2 text-gray-700 hover:bg-gray-200"
            >Services</a
          >
          <a
            href="#"
            class="block px-4 py-2 text-gray-700 hover:bg-gray-200"
            >Contact</a
          >
        </nav>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        isOpen: false,
      };
    },
    methods: {
      toggleSidebar() {
        this.isOpen = !this.isOpen;
      },
    },
  };
  </script>
  
  <style scoped>
  /* Add any additional styles if necessary */
  </style>

