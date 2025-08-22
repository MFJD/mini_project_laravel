<script setup>
import { onMounted, ref, computed } from "vue";
import moment from "moment";
import AppLayout from "@/Layouts/AppLayout";
import AddEditDialog from "./Partials/AddEditDialog";
import DeleteDialog from "./Partials/DeleteDialog";
import Table from "@/Components/Common/Table";
import DateRangePicker from "@/Components/Common/DateTimePickers/DateRangePicker.vue";
import Input from "@/Components/Common/Input";

const format = "YYYY-MM-DD HH:mm";

const props = defineProps({
  events: { type: Array, default: [] },
  starts_at: String,
  ends_at: String,
});

// Date range filter
const dateFilters = ref([null, null]);

onMounted(() => {
  if (props.starts_at) dateFilters.value[0] = moment(props.starts_at, format);
  if (props.ends_at) dateFilters.value[1] = moment(props.ends_at, format);
});

// Search query
const searchQuery = ref("");

// Dialog state
const itemToEdit = ref(null);
const itemToDelete = ref(null);

// Filtered events (date + time + search)
const filteredEvents = computed(() => {
  return props.events.filter((event) => {
    const eventStart = moment(event.starts_at);
    const eventEnd = moment(event.ends_at);
    const [filterStart, filterEnd] = dateFilters.value;

    // Start date filter
    if (filterStart && eventStart.isBefore(filterStart)) {
      return false;
    }

    // End date filter
    if (filterEnd && eventEnd.isAfter(filterEnd)) {
      return false;
    }

    // Search filter (case-insensitive)
    if (searchQuery.value.trim() !== "" &&
        !event.title.toLowerCase().includes(searchQuery.value.trim().toLowerCase())) {
      return false;
    }

    return true;
  });
});


</script>

<template>
  <AppLayout title="Events">
    <template #header>
      <h2 class="font-semibold text-xl text-gray-800 leading-tight">Events</h2>
    </template>

    <div class="card p-4 space-y-4">
      <!-- Filters + Add button -->
      <div class="flex justify-between gap-4">
        <!-- Search input -->
        <div class="flex items-center gap-5">
          <div class="w-[380px] -mt-1">
            <Input
              v-model="searchQuery"
              placeholder="Search title..."
              name="search"
              class="!h-11 !text-sm rounded-md shadow-sm"
            />
          </div>

          <!-- Date range picker -->
          <div class="min-w-[240px]">
            <DateRangePicker v-model="dateFilters" />
          </div>
        </div>

        <!-- Add event button -->
        <div>
          <AddEditDialog :item-to-edit="itemToEdit" @close="itemToEdit = null" />
        </div>
      </div>

      <!-- Delete dialog -->
      <DeleteDialog :item-to-delete="itemToDelete" @close="itemToDelete = null" />

      <!-- Table -->
      <Table :data="filteredEvents" :headings="['Title', 'Start Date', 'End Date', 'Actions']">
        <template #row="{ item }">
          <td>{{ item.title }}</td>
          <td>{{ moment(item.starts_at).format("HH:mm DD/MM/YYYY") }}</td>
          <td>{{ moment(item.ends_at).format("HH:mm DD/MM/YYYY") }}</td>
          <td>
            <span
              class="px-2 text-gray-700 hover:text-blue-500 cursor-pointer transition"
              @click="itemToEdit = item"
            >
              <vue-feather type="edit" size="1.3rem" />
            </span>
            <span
              class="px-2 text-gray-700 hover:text-red-500 cursor-pointer transition"
              @click="itemToDelete = item"
            >
              <vue-feather type="trash" size="1.3rem" />
            </span>
          </td>
        </template>

        <template #empty>
          <tr>
            <td colspan="4" class="text-center py-6 font-thin text-xl text-gray-500">
              No data
            </td>
          </tr>
        </template>
      </Table>
    </div>
  </AppLayout>
</template>

<style scoped>
/* Optional: make input smaller and nicer */
input.p-3 {
  @apply px-3 py-2 !text-sm !h-10 rounded-md shadow-sm;
}
</style>
