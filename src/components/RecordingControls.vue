<template>
  <div class="recording-controls">
    <div v-if="isRecording" class="record-status">
      <span class="record-icon">
        <span class="animate-ping"></span>
        <span class="icon"></span>
      </span>
      {{ formattedRecordDuration }}
    </div>
    <button @click="toggleRecording" class="record-button">
      <div :class="isRecording ? 'recording-active' : 'recording-inactive'"></div>
    </button>
  </div>
</template>

<script setup>
const props = defineProps({
  isRecording: Boolean,
  formattedRecordDuration: String
});

const emit = defineEmits(['toggle']);

const toggleRecording = () => {
  emit('toggle');
};
</script>

<style scoped>
.recording-controls {
  display: flex;
  align-items: center;
  gap: 16px;
}

.record-status {
  display: flex;
  align-items: center;
  gap: 8px;
}

.record-icon {
  position: relative;
  display: inline-flex;
  height: 12px;
  width: 12px;
}

.animate-ping {
  position: absolute;
  height: 100%;
  width: 100%;
  border-radius: 50%;
  background: red;
  opacity: 0.75;
  animation: ping 1s infinite;
}

.icon {
  position: relative;
  height: 12px;
  width: 12px;
  border-radius: 50%;
  background: red;
}

@keyframes ping {
  0% {
    transform: scale(1);
  }
  100% {
    transform: scale(2);
    opacity: 0;
  }
}

.record-button {
  cursor: pointer;
  height: 48px;
  aspect-ratio: 1 / 1;
  border-radius: 50%;
  background-color: white;
  display: flex;
  align-items: center;
  justify-content: center;
}

.recording-active {
  height: 82%;
  border-radius: 50%;
  background: red;
  transition: all 0.2s ease;
}

.recording-inactive {
  height: 20px;
  border-radius: 5px;
  background: red;
  transition: all 0.2s ease;
}
</style>
