<template>
  <div :style="selectedAspectRatio.style" class="relative-auto">
    <div class="relative h-full w-full overflow-hidden">
      <video ref="videoRecording" autoplay playsinline class="video-js h-full w-full object-cover rounded-2xl overflow-hidden"></video>

      <div v-if="!isRecording" class="absolute top-3 right-2 flex flex-col items-end gap-1 w-28">
        <AspectRatioMenu :aspectRatios="aspectRatios" :selectedAspectRatio="selectedAspectRatio" @select="setAspectRatio" />
      </div>

      <div class="absolute bottom-5 left-0 right-0 m-auto flex items-center justify-center gap-3 px-2 h-[70px] w-full">
        <DeviceSelector v-if="!isRecording && currentVideoDevice" :sources="sources.video" :selectedDevice="currentVideoDevice" :sourceType="'Video'" />

        <div @click="toggleRecording" class="relative cursor-pointer bg-white rounded-full h-[48px] aspect-square flex items-center justify-evenly">
          <div v-if="isRecording" class="absolute flex items-center gap-2 bottom-16 w-max">
            <span class="relative inline-flex h-3 w-3">
              <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-red-600 opacity-75"></span>
              <span class="relative inline-flex h-3 w-3 rounded-full bg-red-600"></span>
            </span>
            {{ computedRecordDuration }}
          </div>
          <div :class="isRecording ? 'small' : 'large'" class="bg-red-700 border-2 border-solid"></div>
        </div>

        <DeviceSelector v-if="!isRecording && currentAudioDevice" :sources="sources.audio" :selectedDevice="currentAudioDevice" :sourceType="'Audio'" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import RecordRTC from "recordrtc";
import AspectRatioMenu from "./AspectRatioMenu.vue";
import DeviceSelector from "./DeviceSelector.vue";

const props = defineProps({
  maxDuration: { required: true }
});

const aspectRatios = ref([
  { label: "9:16", ratio: 9 / 16, style: "aspect-ratio: 9 / 16", tailwind: "inline-block border-[1px] h-4 aspect-[9/14]" },
  { label: "16:9", ratio: 16 / 9, style: "aspect-ratio: 16 / 9", tailwind: "inline-block border-[1px] h-3 aspect-[16/9]" },
  { label: "1:1", ratio: 1 / 1, style: "aspect-ratio: 1 / 1", tailwind: "inline-block border-[1px] h-3 aspect-square" }
]);

const selectedAspectRatio = ref(aspectRatios.value[0]);
const currentAudioDevice = ref(null);
const currentVideoDevice = ref(null);
const isRecording = ref(false);
const videoRecording = ref(null);
const recorder = ref(null);
const counter = ref(0);
const sources = ref({ video: [], audio: [] });
const emit = defineEmits(["set-video", "handle-error"]);

const computedRecordDuration = computed(() => `00:${String(counter.value).padStart(2, '0')}`);

onMounted(() => {
  initializeDevices();
});

const initializeDevices = async () => {
  try {
    const devices = await navigator.mediaDevices.enumerateDevices();
    sources.value.video = devices.filter(device => device.kind === "videoinput");
    sources.value.audio = devices.filter(device => device.kind === "audioinput");
    currentAudioDevice.value = sources.value.audio[0]?.deviceId;
    currentVideoDevice.value = sources.value.video[0]?.deviceId;
    updateSource();
  } catch (error) {
    emit("handle-error", error);
  }
};

const updateSource = async () => {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({
      audio: { deviceId: currentAudioDevice.value },
      video: { deviceId: currentVideoDevice.value, aspectRatio: selectedAspectRatio.value.ratio }
    });
    videoRecording.value.srcObject = stream;
  } catch (error) {
    emit("handle-error", error);
  }
};

const toggleRecording = () => {
  isRecording.value = !isRecording.value;
  if (isRecording.value) {
    startRecording();
  } else {
    stopRecording();
  }
};

const startRecording = () => {
  videoRecording.value.muted = true;
  videoRecording.value.volume = 0;
  const mimeType = window.navigator.userAgent.includes("Firefox") ? "video/webm; codecs=vp9" : "video/webm; codecs=vp8";
  recorder.value = RecordRTC(videoRecording.value.srcObject, { type: "video", mimeType });
  recorder.value.startRecording();
  startCounter();
};

const stopRecording = () => {
  stopCounter();
  recorder.value.stopRecording(() => {
    const blob = recorder.value.getBlob();
    recorder.value.destroy();
    recorder.value = null;
    emit("set-video", { blob, aspectRatio: selectedAspectRatio.value.style });
  });
};

const startCounter = () => {
  counter.value = 0;
  const intervalId = setInterval(() => {
    counter.value++;
    if (counter.value >= props.maxDuration) {
      clearInterval(intervalId);
      stopRecording();
    }
  }, 1000);
};

const stopCounter = () => {
  counter.value = 0;
};

const setAspectRatio = (aspectRatio) => {
  selectedAspectRatio.value = aspectRatio;
  updateSource();
};
</script>
