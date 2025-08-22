


<script setup>
import { ref } from "vue";
import moment from "moment";
import Calendar from "./Partials/CalendarPopup.vue";

const format = "DD/MM/YYYY HH:mm";
const emit = defineEmits(["update:modelValue"]);

const props = defineProps({
  modelValue: {
    type: Array, // [moment|null, moment|null]
    validator: (value) => value?.every((d) => !d || moment.isMoment(d)),
    default: () => [null, null],
  },
});

const endDatePicker = ref(null);
const showStartDatePopup = ref(false);
const showEndDatePopup = ref(false);

// START
const onStartDateChange = (date) => {
  emit("update:modelValue", [date ? date.clone() : null, props.modelValue?.[1]]);
};
const openStart = () => { showStartDatePopup.value = true; };
const closeStart = () => { showStartDatePopup.value = false; };

// END
const onEndDateChange = (date) => {
  emit("update:modelValue", [props.modelValue?.[0], date ? date.clone() : null]);
};
const openEnd = () => { showEndDatePopup.value = true; };
const closeEnd = () => { showEndDatePopup.value = false; };
</script>

<template>
  <div class="grid grid-cols-2 gap-2">
    <!-- START -->
    <button
      type="button"
      class="date-button rounded-l-md relative"
      @click="openStart"
    >
      <span class="block truncate">
        {{ modelValue?.[0] ? modelValue[0].format(format) : "Start date & time" }}
      </span>

      <Calendar
        :show="showStartDatePopup"
        :value="modelValue?.[0]"
        :with-date="true"
        :with-time="true"
        @change="onStartDateChange"
        @close="closeStart"
      />
    </button>

    <!-- END -->
    <button
      ref="endDatePicker"
      type="button"
      class="date-button rounded-r-md relative"
      @click="openEnd"
    >
      <span class="block truncate">
        {{ modelValue?.[1] ? modelValue[1].format(format) : "End date & time" }}
      </span>

      <Calendar
        :show="showEndDatePopup"
        :value="modelValue?.[1]"
        :with-date="true"
        :with-time="true"
        @change="onEndDateChange"
        @close="closeEnd"
      />
    </button>
  </div>
</template>

<style scoped>
.date-button {
  @apply px-3 h-11 w-full border border-gray-300 focus:border-indigo-300 
         focus:ring focus:ring-indigo-200 focus:ring-opacity-50 
         text-left bg-white rounded-md shadow-sm;
}
</style>
