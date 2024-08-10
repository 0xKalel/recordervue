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
      @error="handleError"
      :maxDuration="maxDuration"
      :style="'max-height:'+maxHeight+'px'"
    />
  </div>
</template>

<script setup>
import { ref, reactive, nextTick, watch } from 'vue';
import Recorder from '../components/Recorder.vue';
import mime from 'mime-types';

const props = defineProps({
  maxDuration:{
    type: Number,
    required:true
  },
  maxHeight:{
    type: Number,
    required:true
  },
  maxFileSize:{
    type: Number,
    required:true
  },
});

const isRecording = ref(false);
const videoAspectRatio = ref(null);
const videoSourceUrl = ref('');
const videoElement = ref(null);

const videoData = reactive({
  file: null,
  isRecorded: false,
  mimeType: null
});

const emit = defineEmits(['error', 'videoRecorded']);

const handleError = (error) => {
  emit('error', error);
};

const resetVideoData = () => {
  videoData.file = null;
  videoData.isRecorded = false;
  videoData.mimeType = null;
  videoAspectRatio.value = null;
  videoSourceUrl.value = '';

  if (videoElement.value) {
    videoElement.value.src = '';
  }

  if (videoSourceUrl.value) {
    URL.revokeObjectURL(videoSourceUrl.value);
    videoSourceUrl.value = '';
  }
};

const handleRecordedVideo = async (data) => {
  videoData.isRecorded = true;
  isRecording.value = false;
  videoAspectRatio.value = data.aspectRatio;
  videoData.mimeType = data.blob.type;

  const extension = mime.extension(videoData.mimeType);
  const filename = `record_${Date.now()}.${extension}`;
  const mediaBlob = await fetch(URL.createObjectURL(data.blob)).then(response => response.blob());
  videoData.file = new File([mediaBlob], filename, { type: videoData.mimeType });

  await nextTick();
  videoSourceUrl.value = URL.createObjectURL(videoData.file);
  
  emit('videoRecorded', videoData);
};

const validateVideoFile = () => {
  const { duration, videoWidth, videoHeight } = videoElement.value || {};
  const fileSizeMB = videoData.file.size / (1024 * 1024);

  if (fileSizeMB > props.maxFileSize) {
    resetVideoData();
    handleError(`The video must be less than ${props.maxFileSize}MB.`);
    return;
  }

  if (duration > props.maxDuration) {
    resetVideoData();
    handleError(`The video duration must be less than ${props.maxDuration}s.`);
    return;
  }
};

watch(videoSourceUrl, () => {
  if (videoElement.value) {
    const onLoadedMetadata = () => {
      if (videoElement.value.duration === Infinity) {
        videoElement.value.currentTime = 1e101;
        videoElement.value.ontimeupdate = () => {
          videoElement.value.currentTime = 0;
          videoElement.value.ontimeupdate = null;
          validateVideoFile();
        };
      } else {
        validateVideoFile();
      }
    };
    
    videoElement.value.addEventListener('loadedmetadata', onLoadedMetadata);
    watch(() => videoSourceUrl.value, () => {
      videoElement.value?.removeEventListener('loadedmetadata', onLoadedMetadata);
    });
  }
});

defineExpose({
  resetVideoData,
});
</script>
