<script setup>
import { onMounted, ref } from "vue";
import moment from "moment";
import AppLayout from "@/Layouts/AppLayout";
import AddEditDialog from "./Partials/AddEditDialog";
import DeleteDialog from "./Partials/DeleteDialog";
import Table from "@/Components/Common/Table";

const format = "YYYY-MM-DD";

const props = defineProps({
  events: {
    type: Array,
    default: [],
  },
  starts_at: String,
  ends_at: String,
});

const dateFilters = ref([null, null]);

onMounted(() => {
  if (props.starts_at) {
    dateFilters.value[0] = moment(props.starts_at, format);
  }
  if (props.ends_at) {
    dateFilters.value[1] = moment(props.ends_at, format);
  }
});

const itemToEdit = ref(null);
const itemToDelete = ref(null);
</script>

<template>
  <AppLayout title="Events">
    <template #header>
      <h2 class="font-semibold text-xl text-gray-800 leading-tight">Events</h2>
    </template>

    <div class="card">
      <div class="mb-3">
        <div class="mb-6 flex flex-row justify-between items-end">
          <div>
            <AddEditDialog :item-to-edit="itemToEdit" @close="itemToEdit = null" />
          </div>
        </div>

        <!-- New delete dialog -->
        <DeleteDialog :item-to-delete="itemToDelete" @close="itemToDelete = null" />
      </div>

      <Table :data="events" :headings="['Title', 'Start Date', 'End Date', 'Actions']">
        <template #row="{ item }">
          <td>{{ item.title }}</td>
          <td>{{ moment(item.starts_at).format("HH:mm DD/MM/YYYY") }}</td>
          <td>{{ moment(item.ends_at).format("HH:mm DD/MM/YYYY") }}</td>
          <td>
            <span
              class="px-2 text-gray-700 hover:text-blue-500 cursor-pointer transition"
            >
              <vue-feather type="edit" size="1.3rem" @click="itemToEdit = item" />
            </span>
            <span
              class="px-2 text-gray-700 hover:text-red-500 cursor-pointer transition"
            >
              <vue-feather type="trash" size="1.3rem" @click="itemToDelete = item" />
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
