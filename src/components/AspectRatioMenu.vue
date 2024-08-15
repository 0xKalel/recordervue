<template>
  <button type="button" @click="toggleMenu"
    class="aspect-ratio-button inline-flex items-center gap-1 border-2 border-black bg-black hover:bg-gray-700 text-white h-5 rounded-xl px-1 py-2">
    <span :class="selectedAspectRatio.tailwind" class="border-white transform scale-75"></span>
    <span class="text-xs">Ratio</span>
  </button>

  <!-- Aspect Ratios Dropdown Menu -->
  <div v-if="isMenuOpen"
    class="aspect-ratio-menu bg-black divide-y divide-gray-100 rounded-b-lg rounded-tl-lg shadow w-full dark:bg-gray-700">
    <ul class="py-2 text-sm text-white dark:text-white">
      <li v-for="(aspectRatio, index) in aspectRatios" :key="index" @click="selectAspectRatio(aspectRatio)"
        class="aspect-ratio-item flex flex-row-reverse items-center justify-between px-2 py-2 hover:bg-gray-700 cursor-pointer">
        <span class="border-white" :class="aspectRatio.tailwind"></span>
        <span>{{ aspectRatio.label }}</span>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from "vue";

const props = defineProps({
  aspectRatios: {
    type: Array,
    required: true,
  },
  selectedAspectRatio: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(["select"]);

const isMenuOpen = ref(false);

const toggleMenu = () => {
  isMenuOpen.value = !isMenuOpen.value;
};

const selectAspectRatio = (ratio) => {
  emit("select", ratio);
  isMenuOpen.value = false;
};
</script>

<style scoped>
.aspect-ratio-button {
  cursor: pointer;
}

.aspect-ratio-menu {
  position: absolute;
  z-index: 1000;
}

.aspect-ratio-item {
  padding: 8px;
  cursor: pointer;
}

.aspect-ratio-item:hover {
  background-color: #f0f0f0;
}
</style>
