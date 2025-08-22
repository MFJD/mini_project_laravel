<script setup>
import { ref, watch } from "vue";
import { useForm } from "@inertiajs/inertia-vue3";
import Dialog from "@/Components/Common/DialogModal";
import Button from "@/Components/Common/Button";
import DateTimePicker from "@/Components/Common/DateTimePickers/DateTimePicker.vue";
import Input from "@/Components/Common/Input";
import moment from "moment";

const emit = defineEmits(["close"]);

const props = defineProps({
  itemToEdit: {
    type: Object,
    default: null,
  },
});

const show = ref(false);
const editing = ref(false);

const form = useForm({
  title: "",
  starts_at: null,
  ends_at: null,
});

watch(() => props.itemToEdit, (newVal) => {
  if (newVal) {
    editing.value = true;
    form.title = newVal.title;
    form.starts_at = moment(newVal.starts_at);
    form.ends_at = moment(newVal.ends_at);
    show.value = true;
  }
});

const onAddNew = () => {
  form.reset();
  show.value = true;
  editing.value = false;
};

const onSubmit = () => {
  delete form.errors.ends_at;

  if (moment(form.starts_at).isAfter(moment(form.ends_at))) {
    form.errors.ends_at = "End date/time must be after start date/time.";
    return;
  }

  const transform = (data) => ({
    ...data,
    starts_at: moment(data.starts_at).format("YYYY-MM-DD HH:mm"),
    ends_at: moment(data.ends_at).format("YYYY-MM-DD HH:mm"),
  });

  const requestParams = {
    preserveScroll: true,
    onSuccess: onClose,
  };

  if (editing.value && props.itemToEdit) {
    form.transform(transform).put(route("events.update", props.itemToEdit.id), requestParams);
  } else {
    form.transform(transform).post(route("events.store"), requestParams);
  }
};

const onClose = () => {
  form.reset();
  show.value = false;
  emit("close");
};
</script>

<template>
  <div>
    <Button @click="onAddNew">
      <vue-feather type="plus" />
      <span class="ml-2">Add new</span>
    </Button>

    <Dialog :show="show" @close="onClose">
      <template #header>
        {{ editing ? "Edit event" : "Add new event" }}
      </template>

      <Input name="title" label="Title" v-model="form.title" class="mb-2" />
      <div v-if="form.errors.title" class="text-red-500 mb-4">{{ form.errors.title }}</div>

      <DateTimePicker label="Start date/time" v-model="form.starts_at" class="mb-2" />
      <div v-if="form.errors.starts_at" class="text-red-500 mb-4">{{ form.errors.starts_at }}</div>

      <DateTimePicker label="End date/time" v-model="form.ends_at" class="mb-2" />
      <div v-if="form.errors.ends_at" class="text-red-500 mb-4">{{ form.errors.ends_at }}</div>

      <template #footer>
        <Button variant="secondary" class="mr-3" @click="onClose">Cancel</Button>
        <Button @click="onSubmit" :disabled="form.processing">Submit</Button>
      </template>
    </Dialog>
  </div>
</template>
