<template>
  <div class="video-container w-fit">
    <video
      ref="videoElement"
      controls
      v-if="videoData.file"
    >
      <source :src="videoSourceUrl" :type="videoData.mimeType" />
      Your browser does not support the video tag.
    </video>
    <Recorder
      v-else
      @set-video="handleRecordedVideo"
      @error="emitError"
      :maxDuration="maxDuration"
      :style="{ maxHeight: maxHeight + 'px' }"
    />
  </div>
</template>

<script setup>
import { ref, reactive, nextTick, watch } from 'vue';
import Recorder from '../components/Recorder.vue';
import mime from 'mime-types';

const props = defineProps({
  maxDuration: { type: Number, required: true },
  maxHeight: { type: Number, required: true },
  maxFileSize: { type: Number, required: true },
});

const emit = defineEmits(['error', 'videoRecorded']);

const videoElement = ref(null);
const videoSourceUrl = ref('');
const videoData = reactive({ file: null, mimeType: null });
const isRecording = ref(false);

const resetVideoData = () => {
  videoData.file = null;
  videoData.mimeType = null;
  videoSourceUrl.value && URL.revokeObjectURL(videoSourceUrl.value);
  videoSourceUrl.value = '';
};

const emitError = (message) => emit('error', message);

const validateVideoFile = () => {
  const { duration } = videoElement.value || {};
  const fileSizeMB = videoData.file.size / (1024 * 1024);

  if (fileSizeMB > props.maxFileSize) {
    resetVideoData();
    return emitError(`Video size must be less than ${props.maxFileSize}MB.`);
  }

  if (duration > props.maxDuration) {
    resetVideoData();
    return emitError(`Video duration must be less than ${props.maxDuration}s.`);
  }
};

const handleRecordedVideo = async (data) => {
  const mimeType = data.blob.type;
  const filename = `record_${Date.now()}.${mime.extension(mimeType)}`;
  const mediaBlob = await fetch(URL.createObjectURL(data.blob)).then(res => res.blob());
  videoData.file = new File([mediaBlob], filename, { type: mimeType });

  await nextTick();
  videoSourceUrl.value = URL.createObjectURL(videoData.file);

  validateVideoFile();
  emit('videoRecorded', videoData);
};

watch(videoSourceUrl, (newUrl, oldUrl) => {
  if (newUrl && videoElement.value) {
    const handleMetadataLoaded = () => validateVideoFile();
    videoElement.value.addEventListener('loadedmetadata', handleMetadataLoaded);
    watch(() => videoSourceUrl.value, () => {
      videoElement.value?.removeEventListener('loadedmetadata', handleMetadataLoaded);
    });
  }
});

defineExpose({ resetVideoData });
</script>
