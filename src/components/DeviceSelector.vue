<template>
    <select v-if="sources && sources.length > 0" :value="selectedDevice" @change="updateSource"
        class="remove-arrow bg-black text-white text-xs h-6 w-28 cursor-pointer rounded-xl hover:bg-gray-800">
        <option v-for="(source, index) in sources" :key="index" :value="source.deviceId">
            {{ source.label }}
        </option>
    </select>
    <button v-else
        class="remove-arrow bg-black text-white text-xs h-6 w-28 cursor-pointer rounded-xl border border-gray-500 hover:bg-gray-800"
        disabled>
        No {{ sourceType }} Available
    </button>
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

const updateSource = (event) => {
    emit('updateSource', event.target.value);
};
</script>

<style scoped>
.source-selector {
    display: flex;
    align-items: center;
}

.source-select,
.no-source-button {
    background-color: black;
    color: white;
    padding: 4px 8px;
    border-radius: 8px;
    cursor: pointer;
}

.no-source-button {
    cursor: not-allowed;
}
</style>