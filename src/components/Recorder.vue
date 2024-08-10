<template>
  <div class="relative-auto" :style="selectedAspectRatio.style">
    <div class="relative h-full w-full overflow-hidden">

      <video ref="videoRecoring" autoplay playsinline
        class="video-js rounded-2xl overflow-hidden h-full w-full object-cover"></video>

      <div v-if="!isRecording" class="absolute top-3 right-2 w-28 flex flex-col items-end gap-1">
        <AspectRatioMenu :aspectRatios="aspectRatios" :selectedAspectRatio="selectedAspectRatio"
          @select="setAspectRatio"></AspectRatioMenu>
      </div>

      <div class="absolute bottom-5 left-0 right-0 m-auto w-full h-[70px] px-2 flex items-center justify-center gap-3">

        <DeviceSelector v-if="!isRecording && currentVideoDevice" :sources="sources.video"
          :selectedDevice="currentVideoDevice" :sourceType="'Video'"></DeviceSelector>

        <div @click="toggleRecording()"
          class=" relative cursor-pointer record rounded-full h-[48px] aspect-square bg-white flex justify-evenly items-center">

          <div v-if="isRecording" class="absolute bottom-16 w-max flex gap-2 items-center">
            <span class="relative inline-flex h-3 w-3">
              <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-red-600 opacity-75"></span>
              <span class="relative inline-flex rounded-full h-3 w-3 bg-red-600"></span>
            </span>
            {{ computedRecordDuration }}
          </div>

          <div class="bg-red-700 border-2 border-solid" :class="isRecording ? 'small' : 'large'"></div>

        </div>

        <DeviceSelector v-if="!isRecording && currentAudioDevice" :sources="sources.audio"
          :selectedDevice="currentAudioDevice" :sourceType="'Audio'"></DeviceSelector>

      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, computed } from "vue";
import RecordRTC from "recordrtc";
import AspectRatioMenu from "./AspectRatioMenu.vue";
import DeviceSelector from "./DeviceSelector.vue";

const props = defineProps({ maxDuration: { required: true } });
const aspectRatios = ref([
  { label: "9:16", ratio: 9 / 16, style: "aspect-ratio: 9 / 16", tailwind: "inline-block border-[1px] h-4 aspect-[9/14]" },
  { label: "16:9", ratio: 16 / 9, style: "aspect-ratio: 16 / 9", tailwind: "inline-block border-[1px] h-3 aspect-[16/9]" },
  { label: "1:1", ratio: 1 / 1, style: "aspect-ratio: 1 / 1", tailwind: "inline-block border-[1px] h-3 aspect-square" },
]);

const selectedAspectRatio = ref(aspectRatios.value[0]);
const currentAudioDevice = ref(null);
const currentVideoDevice = ref(null);
const isRecording = ref(false);
const videoRecoring = ref(null);
const recorder = ref(null);
const counter = ref(0);
const sources = ref({ video: [], audio: [] });
const emit = defineEmits(["set-video","handle-error"]);

const computedRecordDuration = computed(() => {
  return `00:${String(counter.value).padStart(2, '0')}`;
});

onMounted(async () => {
  await initializeDevices();
});

const initializeDevices = async () => {
  const devices = await navigator.mediaDevices.enumerateDevices();
  sources.value.video = devices.filter(device => device.kind === "videoinput");
  sources.value.audio = devices.filter(device => device.kind === "audioinput");
  currentAudioDevice.value = sources.value.audio[0]?.deviceId;
  currentVideoDevice.value = sources.value.video[0]?.deviceId;
  updateSource();
};

const updateSource = async () => {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({
      audio: { deviceId: currentAudioDevice.value },
      video: { deviceId: currentVideoDevice.value, aspectRatio: selectedAspectRatio.value.ratio },
    });
    videoRecoring.value.srcObject = stream;
  } catch (e) {
    emit('handle-error', e)
  }
};

const toggleRecording = () => {
  isRecording.value = !isRecording.value;
  isRecording.value ? startRecording() : stopRecording();
};

const startRecording = () => {
  videoRecoring.value.muted = true;
  videoRecoring.value.volume = 0;
  const userAgent = window.navigator.userAgent;
  let mimeType = 'video/webm; codecs=vp8'
  if (userAgent.includes("Firefox")) {
    mimeType = 'video/webm; codecs=vp9'
  }
  recorder.value = RecordRTC(videoRecoring.value.srcObject, { type: "video", mimeType: mimeType });
  recorder.value.startRecording();
  startCounter();
};

const stopRecording = () => {
  stopCounter();
  recorder.value.stopRecording(() => {
    const blob = recorder.value.getBlob();
    recorder.value.destroy();
    recorder.value = null;
    const data = {
      blob: blob,
      aspectRatio: selectedAspectRatio.value.style
    }
    emit('set-video', data);
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