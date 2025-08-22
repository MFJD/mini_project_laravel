<script setup>
import { computed, ref } from "vue";
import moment from "moment";
import Calendar from "./Partials/CalendarPopup";

const emit = defineEmits(["update:modelValue"]);

const props = defineProps({
  modelValue: {
    type: Object, // moment or null
    validator: (v) => v == null || moment.isMoment(v),
    default: null,
  },
  label: String,
  type: {
    type: String,
    validator: (val) => ["date", "datetime", "time"].includes(val),
    default: "datetime",
  },
});

const showPopup = ref(false);

const hasTime = computed(() => props.type !== "date");
const hasDate = computed(() => props.type !== "time");

const format = computed(() => {
  switch (props.type) {
    case "time": return "HH:mm";
    case "date": return "DD/MM/YYYY";
    default: return "HH:mm DD/MM/YYYY";
  }
});

const open = () => { showPopup.value = true; };
const close = () => { showPopup.value = false; };
</script>

<template>
  <div>
    <label class="block font-medium text-sm text-gray-700 mb-1">
      <span v-if="label">{{ label }}</span>
    </label>

    <div class="relative">
      <button
        type="button"
        class="px-3 h-11 w-full border border-gray-300 focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50 rounded-md shadow-sm text-left bg-white"
        @click="open"
      >
        <span class="block truncate">
          {{ modelValue ? modelValue.format(format) : (type === 'time' ? 'Select time' : type === 'date' ? 'Select date' : '') }}
        </span>

        <Calendar
          :show="showPopup"
          :value="modelValue"
          :with-date="hasDate"
          :with-time="hasTime"
          @change="$emit('update:modelValue', $event)"
          @close="close"
        />
      </button>
    </div>
  </div>
</template>
