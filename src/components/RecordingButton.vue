<template>
    <div @click="toggle" class="relative cursor-pointer record-button rounded-full h-[48px] aspect-square bg-white flex justify-evenly items-center">
      <div v-if="isRecording" class="absolute bottom-16 w-max flex gap-2 items-center">
        <span class="relative inline-flex h-3 w-3">
          <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-red-600 opacity-75"></span>
          <span class="relative inline-flex rounded-full h-3 w-3 bg-red-600"></span>
        </span>
        {{ formattedDuration }}
      </div>
      <div class="bg-red-700 border-2 border-solid" :class="isRecording ? 'recording-active' : 'recording-inactive'"></div>
    </div>
  </template>
  
  <script setup>
  const props = defineProps({
    isRecording: Boolean,
    formattedDuration: String
  });
  
  const emit = defineEmits(['toggle']);
  
  const toggle = () => {
    emit('toggle');
  };
  </script>
  