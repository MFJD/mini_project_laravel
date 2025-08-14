<script setup>
defineProps({
  data: {
    type: Array,
    default: () => [],
  },
  headings: {
    type: Array,
    default: () => [],
  },
});
</script>

<template>
  <table class="w-full text-sm text-left text-gray-500">
    <thead class="text-gray-700 uppercase bg-gray-50">
      <tr>
        <th v-for="(heading, index) in headings" :key="index" class="py-3 px-6">{{ heading }}</th>
      </tr>
    </thead>

    <tbody>
      <!-- Render rows if data exists -->
      <template v-if="data.length">
        <tr v-for="(row, index) in data" :key="index" class="border-b odd:bg-white even:bg-gray-50">
          <slot name="row" :item="row" />
        </tr>
      </template>

      <!-- Render empty slot if no data -->
      <template v-else>
        <slot name="empty">
          <tr>
            <td :colspan="headings.length" class="text-center  py-8 text-2xl text-gray-500">No data</td>
          </tr>
        </slot>
      </template>
    </tbody>
  </table>
</template>

<style scoped></style>
