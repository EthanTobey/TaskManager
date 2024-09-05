<script setup>
import TableEntry from './TableEntry.vue';
import AddPopup from './AddPopup.vue';
</script>

<template>
  <!-- Popup for the add button-->
  <AddPopup
    v-if="isPopupVisible"
    @close="closePopup"
    @form-submitted="handleFormSubmitted"
    @entry-updated="handleEntryUpdate"
    @show-notification="emitNotification"
    :defaultValues="popupDefaultValues"
    :isAddPopup="isAddPopup"
    :editingIndex="editingIndex"
    :formDataTitles="formDataTitles"
  />
  <!-- HTML goes Here-->
  <div class="container-fluid">
    <div class="col-xs-12">
      <!-- Card Starts Here-->
      <div class="card">
        <div class="card-header">
          <div class="row">
            <div class="col">
              <div class="card-title">
                <i class="fa-solid fa-bars"></i> FRAMEWORKS
              </div>
            </div>
            <div class="col-auto">
              <!--in the @click: emit('eventName') sends an event to the parent component which can be handled-->
              <!--comparatively, smthn like @click="methodName" calls a method within THIS component-->
              <button type="button" class="btn btn-primary" @click="showPopup">
                <i class="fa-solid fa-circle-plus"></i> ADD
              </button>
            </div>
          </div>
        </div>

        <div class="card-body">
          <table class="table text-center">
            <thead>
              <tr>
                <th>Title</th>
                <th>Description</th>
                <th>Deadline</th>
                <th>Priority</th>
                <th>Is Complete</th>
                <th>Action</th>
              </tr>
            </thead>
            <tbody>
              <tr
                class="table-row"
                v-for="(entry, index) in formData"
                :key="index"
                :id="index"
              >
                <TableEntry
                  :entryData="entry"
                  @delete-entry="deleteEntry(index)"
                  @show-update-popup="showUpdatePopup(index)"
                  @show-notification="emitNotification"
                />
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isPopupVisible: false,
      isAddPopup: true, //says whether the popup is to add a new component or modify an existing one
      formData: [],
      popupDefaultValues: {
        title: '',
        description: '',
        deadline: '',
        nonFormattedDeadline: new Date(),
        selectedPriority: 'low',
      },
      editingIndex: null, //index of row we want to edit - used to pass props
    };
  },
  computed: {
    //compute list of all the titles from formData
    formDataTitles() {
      return this.formData.map((data) => data.title);
    },
  },
  components: {
    AddPopup,
    TableEntry,
  },
  methods: {
    showPopup() {
      this.isAddPopup = true;
      this.isPopupVisible = true;
    },
    closePopup() {
      this.isPopupVisible = false;

      //reset popupDefaultValues
      this.popupDefaultValues = {
        title: '',
        description: '',
        deadline: '',
        nonFormattedDeadline: new Date(),
        selectedPriority: 'low',
        isComplete: false, //tracks if an entry is marked as complete
      };
    },
    handleFormSubmitted(formData) {
      this.formData.push(formData); //add the formData to the array
    },
    deleteEntry(index) {
      this.formData.splice(index, 1); //remove entry in formData starting from index - remove 1 entry
    },
    showUpdatePopup(index) {
      this.popupDefaultValues = {
        title: this.formData[index].title,
        description: this.formData[index].description,
        deadline: this.formData[index].deadline,
        nonFormattedDeadline: this.formData[index].nonFormattedDeadline,
        selectedPriority: this.formData[index].selectedPriority,
        isComplete: this.formData[index].isComplete,
      };
      this.isAddPopup = false;
      this.isPopupVisible = true;
      this.editingIndex = index;
    },
    handleEntryUpdate(index, data) {
      this.formData[index] = {
        title: data.title,
        description: data.description,
        deadline: data.deadline,
        nonFormattedDeadline: data.nonFormattedDeadline,
        selectedPriority: data.selectedPriority,
        isComplete: data.isComplete,
      };
    },
    emitNotification(type, message) {
      this.$emit('show-notification', type, message);
    },
  },
};
</script>
