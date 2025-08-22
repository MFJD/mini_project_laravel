<script setup>
import { ref, watch } from "vue";
import moment from "moment";

const props = defineProps({
  show: { type: Boolean, default: false },
  value: Object, // moment object (or null)
  withDate: { type: Boolean, default: true },
  withTime: { type: Boolean, default: true },
});

const emit = defineEmits(["change", "close"]);

// Local working copy
const internalDate = ref(props.value ? props.value.clone() : moment());

watch(
  () => props.value,
  (newVal) => {
    internalDate.value = newVal ? newVal.clone() : moment();
  }
);

// Date change
const onDateChange = (event) => {
  const dateStr = event.target.value;
  if (!props.withDate || !dateStr) return;

  const date = moment(dateStr, "YYYY-MM-DD");

  if (props.withTime && internalDate.value) {
    // Preserve hour & minute exactly
    date.set({
      hour: internalDate.value.hour(),
      minute: internalDate.value.minute(),
      second: 0,
      millisecond: 0
    });
  } else {
    // If no time, set to 00:00
    date.set({ hour: 0, minute: 0, second: 0, millisecond: 0 });
  }

  internalDate.value = date.clone();
  emit("change", internalDate.value);
};

// Time change
const onHourChange = (h) => {
  if (!props.withTime) return;

  internalDate.value = (internalDate.value || moment()).clone().set({
    hour: h,
    second: 0,
    millisecond: 0
  });
  emit("change", internalDate.value);
};

const onMinuteChange = (m) => {
  if (!props.withTime) return;

  internalDate.value = (internalDate.value || moment()).clone().set({
    minute: m,
    second: 0,
    millisecond: 0
  });
  emit("change", internalDate.value);
};

const hours = Array.from({ length: 24 }, (_, i) => i);
const minutes = Array.from({ length: 60 }, (_, i) => i);
</script>

<template>
  <div
    v-if="show"
    class="absolute z-50 bg-white border rounded shadow p-4 w-72"
    @mousedown.stop
    @click.stop
  >
    <!-- Date selector -->
    <div v-if="withDate" class="mb-4">
      <label class="block text-sm font-medium text-gray-700 mb-1">Date</label>
      <input
        type="date"
        :value="internalDate ? internalDate.format('YYYY-MM-DD') : ''"
        @input="onDateChange"
        class="border rounded px-2 py-1 w-full"
      />
    </div>

    <!-- Time selector -->
    <div v-if="withTime" class="flex space-x-3 items-start mb-4">
      <div class="flex-1">
        <label class="block text-sm font-medium text-gray-700 mb-1">Hour</label>
        <div class="border rounded max-h-32 overflow-y-auto">
          <button
            v-for="h in hours"
            :key="h"
            type="button"
            @click="onHourChange(h)"
            :class="[
              'px-2 py-1 w-full text-left hover:bg-gray-200',
              { 'bg-indigo-100': internalDate && internalDate.hour() === h }
            ]"
          >
            {{ h.toString().padStart(2, '0') }}
          </button>
        </div>
      </div>

      <div class="flex-1">
        <label class="block text-sm font-medium text-gray-700 mb-1">Minute</label>
        <div class="border rounded max-h-32 overflow-y-auto">
          <button
            v-for="m in minutes"
            :key="m"
            type="button"
            @click="onMinuteChange(m)"
            :class="[
              'px-2 py-1 w-full text-left hover:bg-gray-200',
              { 'bg-indigo-100': internalDate && internalDate.minute() === m }
            ]"
          >
            {{ m.toString().padStart(2, '0') }}
          </button>
        </div>
      </div>
    </div>

    <!-- Close button -->
    <div class="flex justify-end">
      <button
        type="button"
        class="px-3 py-1 text-sm rounded bg-gray-200 hover:bg-gray-300"
        @click.stop="$emit('close')"
      >
        Close
      </button>
    </div>
  </div>
</template>

<style scoped>
div::-webkit-scrollbar { width: 6px; }
div::-webkit-scrollbar-thumb {
  background: #cbd5e0;
  border-radius: 3px;
}
div::-webkit-scrollbar-thumb:hover { background: #a0aec0; }
</style>
