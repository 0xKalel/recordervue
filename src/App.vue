<script setup>
import { ref } from 'vue';
import Player from './components/Player.vue';

const props = defineProps({
  videoConfig: {
    type: Object,
    default: () => ({
      maxDuration: 4,
      maxFileSize: 200,
      maxHeight: 500
    })
  }
});

const playerRef = ref(null);

const resetPlayer = () => {
  playerRef.value?.resetVideoData();
};

const onVideoRecorded = (videoData) => {
  console.log('Video data:', videoData);
};

const onError = (message) => {
  console.error('Error message:', message);
};
</script>

<template>
  <div class="h-full w-full">
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
      class="bg-white hover:bg-gray-100 text-gray-800 font-semibold py-2 px-4 border border-gray-400 rounded shadow m-auto w-fit mt-2 cursor-pointer"
    >
      Clear
    </div>
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
