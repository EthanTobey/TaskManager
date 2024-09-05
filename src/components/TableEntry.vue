<script setup></script>
<!-- A component to represent -->
<template>
  <td>{{ entryData.title }}</td>
  <td>{{ entryData.description }}</td>
  <td>{{ entryData.deadline }}</td>
  <td>{{ entryData.selectedPriority }}</td>
  <td>
    <div>
      <label>
        <!-- v-model binds checkbox state to isChecked field - @change binds change event to handleChange() method-->
        <input type="checkbox" v-model="entryData.isComplete" />
      </label>
    </div>
  </td>
  <td>
    <!-- v-if="!isChecked conditionally renders update button based on if check box is checked"-->
    <div v-if="!entryData.isComplete" class="row">
      <div class="col text-center">
        <button
          type="button"
          class="btn btn-primary action-button"
          @click="showUpdatePopup"
        >
          <i class="fa-solid fa-pen-to-square"></i>
          UPDATE
        </button>
      </div>
    </div>
    <div class="row">
      <div class="col text-center">
        <button
          type="button"
          class="btn btn-danger action-button"
          @click="deleteEntry"
        >
          <i class="fa-solid fa-trash"></i>DELETE
        </button>
      </div>
    </div>
  </td>
</template>

<script>
export default {
  props: {
    entryData: {
      type: Object,
      required: true,
    },
  },
  methods: {
    deleteEntry() {
      this.$emit('delete-entry', this.entryData);
      //success toastr
      this.emitNotification('success', 'Task Deleted Successfully');
    },
    showUpdatePopup() {
      this.$emit('show-update-popup', this.entryData);
    },
    emitNotification(type, message) {
      this.$emit('show-notification', type, message);
    },
  },
};
</script>
