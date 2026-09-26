<script setup>
import { ref } from 'vue';
import Player from './components/Player.vue';

const props = defineProps({
  videoConfig: {
    type: Object,
    default: () => ({
      maxDuration: 30,
      maxFileSize: 200,
      maxHeight: 500
    })
  }
});

const playerRef = ref(null);
const errorMessage = ref('');

const resetPlayer = () => {
  errorMessage.value = '';
  playerRef.value?.resetVideoData();
};

const onVideoRecorded = (videoData) => {
  console.log('Video data:', videoData);
};

const onError = (error) => {
  console.error('Recorder error:', error);
  const name = error?.name || '';
  if (name === 'NotAllowedError' || name === 'PermissionDeniedError') {
    errorMessage.value = 'Camera access was denied. Allow camera and microphone access in your browser, then reload the page.';
  } else if (name === 'NotFoundError' || name === 'DevicesNotFoundError') {
    errorMessage.value = 'No camera was found on this device.';
  } else if (name === 'NotReadableError') {
    errorMessage.value = 'The camera is in use by another application.';
  } else {
    errorMessage.value = typeof error === 'string' ? error : 'Could not start the camera: ' + (error?.message || 'unknown error');
  }
};
</script>

<template>
  <div class="min-h-screen flex flex-col bg-gray-50 text-gray-900">
    <header class="px-6 pt-8 pb-2 text-center">
      <h1 class="text-2xl font-bold tracking-tight">recordervue</h1>
      <p class="mt-1 text-sm text-gray-500">
        In-browser video recording for Vue 3 — recordings never leave your browser.
      </p>
    </header>

    <main class="flex-1 px-4 pb-8">
      <div
        v-if="errorMessage"
        class="mx-auto mt-10 max-w-md rounded-lg border border-red-300 bg-red-50 px-5 py-4 text-center text-sm text-red-800"
        role="alert"
      >
        <p>{{ errorMessage }}</p>
        <button
          @click="resetPlayer"
          class="mt-3 rounded border border-red-300 bg-white px-4 py-1.5 font-semibold text-gray-800 hover:bg-gray-100"
        >
          Try again
        </button>
      </div>

      <template v-else>
        <Player
          ref="playerRef"
          class="recorder"
          :maxFileSize="props.videoConfig.maxFileSize"
          :maxDuration="props.videoConfig.maxDuration"
          :maxHeight="props.videoConfig.maxHeight"
          @videoRecorded="onVideoRecorded"
          @error="onError"
        />

        <div
          @click="resetPlayer"
          class="bg-white hover:bg-gray-100 text-gray-800 font-semibold py-2 px-4 border border-gray-400 rounded shadow m-auto w-fit mt-4 cursor-pointer"
        >
          Clear
        </div>
      </template>
    </main>

    <footer class="px-6 pb-6 text-center text-sm text-gray-500">
      Recordings stop automatically after {{ props.videoConfig.maxDuration }}s ·
      <a href="https://github.com/0xKalel/recordervue" class="underline underline-offset-2 hover:text-gray-800">source on GitHub</a>
    </footer>
  </div>
</template>

<style scoped>
.recorder {
  max-height: 500px;
  margin: auto;
  margin-top: 20px;
}

.hover\:bg-gray-100:hover {
  background-color: #f7fafc;
}
</style>
