<template>
  <button type="button" @click="toggleMenu"
    class="inline-flex items-center gap-1 border-2 border-black bg-black hover:bg-gray-700 text-white content-center h-5 rounded-xl px-1 py-2">
    <span :class="selectedAspectRatio.tailwind" class="border-white transform scale-75"></span>
    <span class="text-xs">
      Ratio
    </span>
  </button>

  <!-- aspect ratios dropdown menu -->
  <div v-if="isMenuOpen"
    class="bg-black divide-y divide-gray-100 rounded-b-lg rounded-tl-lg shadow w-full dark:bg-gray-700">
    <ul class="py-2 text-sm text-white dark:text-white ">
      <li v-for="(aspectRatio, index) in aspectRatios" :key="index" @click="selectAspectRatio(aspectRatio)"
        class="flex flex-row-reverse items-center content-center justify-between px-2 py-2 hover:bg-gray-700 cursor-pointer">
        <span class="border-white" :class="aspectRatio.tailwind"></span>
        <span>{{ aspectRatio.label }}</span>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from "vue"
const props = defineProps({
  aspectRatios: {
    type: Array,
    required: true
  },
  selectedAspectRatio: {
    type: Object,
    required: true
  }
});
const emit = defineEmits(['select']);

const isMenuOpen = ref(false);

const toggleMenu = () => {
  isMenuOpen.value = !isMenuOpen.value;
};

const selectAspectRatio = (ratio) => {
  emit('select', ratio);
  isMenuOpen.value = false;
};
</script>

<style scoped>
.aspect-ratio-selector {
  position: relative;
}

.aspect-ratio-button {
  cursor: pointer;
}

.aspect-ratio-menu {
  position: absolute;
  background: white;
  border: 1px solid #ccc;
  list-style-type: none;
  padding: 0;
  margin: 0;
  z-index: 1000;
}

.aspect-ratio-menu ul {
  padding: 0;
  margin: 0;
}

.aspect-ratio-menu li {
  padding: 8px;
  cursor: pointer;
}

.aspect-ratio-menu li:hover {
  background-color: #f0f0f0;
}
</style>
