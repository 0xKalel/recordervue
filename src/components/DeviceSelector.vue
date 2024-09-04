<template>
  <div class="source-selector">
    <select
      v-if="hasSources"
      :value="selectedDevice"
      @change="updateSource"
      class="source-select"
    >
      <option
        v-for="source in sources"
        :key="source.deviceId"
        :value="source.deviceId"
      >
        {{ source.label }}
      </option>
    </select>
    <button
      v-else
      class="no-source-button"
      disabled
    >
      No {{ sourceType }} Available
    </button>
  </div>
</template>

<script setup>
const props = defineProps({
  sources: {
    type: Array,
    default: () => [],
  },
  selectedDevice: {
    type: String,
    required: true,
  },
  sourceType: {
    type: String,
    required: true,
  },
});

const emit = defineEmits(['updateSource']);

const hasSources = computed(() => props.sources && props.sources.length > 0);

const updateSource = (event) => {
  emit('updateSource', event.target.value);
};
</script>

<style scoped>
.source-selector {
  display: flex;
  align-items: center;
}

.source-select {
  background-color: black;
  color: white;
  padding: 4px 8px;
  border-radius: 8px;
  cursor: pointer;
  height: 24px;
  width: 112px;
}

.source-select:hover {
  background-color: #2d2d2d;
}

.no-source-button {
  background-color: black;
  color: white;
  padding: 4px 8px;
  border-radius: 8px;
  border: 1px solid #808080;
  cursor: not-allowed;
  height: 24px;
  width: 112px;
}
</style>
