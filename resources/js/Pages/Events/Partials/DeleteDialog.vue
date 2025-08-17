<script setup>
import { Inertia } from "@inertiajs/inertia";
import Button from "@/Components/Common/Button";
import Dialog from "@/Components/Common/DialogModal";

const props = defineProps({
  itemToDelete: {
    type: Object,
    default: null,
  },
});

const emit = defineEmits(["close"]);

const onDelete = () => {
  if (!props.itemToDelete) return;

  Inertia.delete(route("events.destroy", props.itemToDelete.id), {
    preserveScroll: true,
    onSuccess: () => {
      emit("close"); // tell parent to reset itemToDelete
    },
  });
};
</script>

<template>
  <Dialog :show="itemToDelete != null" @close="emit('close')">
    <template #header>Deleting event</template>
    Are you sure you want to delete this event?
    <template #footer>
      <Button variant="secondary" class="mr-3" @click="emit('close')">Cancel</Button>
      <Button variant="danger" @click="onDelete">Submit</Button>
    </template>
  </Dialog>
</template>
