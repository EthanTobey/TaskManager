<script setup>
import Datepicker from 'vue3-datepicker';
import moment from 'moment';
</script>

<template>
  <div class="modal" tabindex="-1" role="dialog" style="display: block">
    <div class="modal-dialog" role="document">
      <form @submit.prevent="handleSubmit">
        <div class="modal-content">
          <div class="modal-header">
            <h5 v-if="isAddPopup" class="modal-title">
              <i class="fa-solid fa-circle-plus"></i> Add Task
            </h5>
            <h5 v-else class="modal-title">
              <i class="fa-solid fa-pen-to-square"></i> Edit Task
            </h5>
          </div>
          <div class="modal-body px-4">
            <!-- @submit.prevent - stops default submit behavior that would reload page, and call handleSubmit method -->

            <div v-if="isAddPopup" class="form-group mb-4">
              <label for="title">Title</label>
              <input
                type="text"
                class="form-control"
                :class="{
                  'is-invalid':
                    !formData.title ||
                    this.formDataTitles.includes(this.formData.title),
                }"
                placeholder="Title"
                id="title"
                v-model="formData.title"
              />
              <div class="invalid-feedback" v-if="!formData.title">
                Title is Required!
              </div>
              <div
                class="invalid-feedback"
                v-if="this.formDataTitles.includes(this.formData.title)"
              >
                Title Must be Unique!
              </div>
            </div>
            <div class="fomr-group mb-4">
              <label for="description">Description</label>
              <input
                type="text"
                class="form-control"
                :class="{ 'is-invalid': !formData.description }"
                placeholder="Description"
                id="description"
                v-model="formData.description"
              />
              <div class="invalid-feedback">Description is Required!</div>
            </div>
            <div class="form-group mb-4">
              <label for="deadline">Deadline</label>
              <div style="position: relative">
                <datepicker
                  v-model="formData.nonFormattedDeadline"
                  inputFormat="MM/dd/yyyy"
                  class="form-control datepicker-input"
                  id="deadline"
                >
                </datepicker>
                <span class="input-group-text">
                  <i class="fa fa-calendar"></i>
                </span>
              </div>
            </div>
            <div class="form-group">
              <label for="priority" class="form-label">Priority</label>
              <br />
              <div class="form-check form-check-inline">
                <input
                  class="form-check-input"
                  type="radio"
                  id="low"
                  name="priority"
                  value="low"
                  v-model="formData.selectedPriority"
                  checked
                />
                <label class="form-check-label" for="low">Low</label>
              </div>
              <div class="form-check form-check-inline">
                <input
                  class="form-check-input"
                  type="radio"
                  id="med"
                  name="priority"
                  value="med"
                  v-model="formData.selectedPriority"
                />
                <label class="form-check-label" for="med">Med</label>
              </div>
              <div class="form-check form-check-inline">
                <input
                  class="form-check-input"
                  type="radio"
                  id="high"
                  name="priority"
                  value="high"
                  v-model="formData.selectedPriority"
                />
                <label class="form-check-label" for="high">High</label>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button
              v-if="isAddPopup"
              type="submit"
              class="btn btn-primary popup-button"
            >
              <i class="fa-solid fa-circle-plus"></i> ADD
            </button>
            <button
              v-else
              type="button"
              class="btn btn-primary popup-button"
              @click="updateEntry"
            >
              <i class="fa-solid fa-pen-to-square"></i> EDIT
            </button>
            <button
              type="button"
              class="btn btn-danger popup-button"
              @click="closeModal"
            >
              <i class="fa-solid fa-ban"></i> CANCEL
            </button>
          </div>
        </div>
      </form>
    </div>
  </div>
  <div class="modal-backdrop fade show"></div>
</template>

<script>
/*The export defualt contians the fucntionality for the component*/
export default {
  components: {
    Datepicker,
  },
  props: {
    defaultValues: {
      type: Array, //type of the defualt values prop
      required: true, //must have these passed in (always will anyway)
      validator: (value) => {
        return value.length === 4; // Ensure the array has exactly 4 values
      },
    },
    isAddPopup: Boolean,
    editingIndex: Object,
    formDataTitles: Array,
  },
  data() {
    return {
      /*put an entry for each input data - bind them in the input with v-model*/
      formData: {
        title: this.defaultValues.title,
        description: this.defaultValues.description,
        deadline: this.defaultValues.deadline,
        nonFormattedDeadline: this.defaultValues.nonFormattedDeadline,
        selectedPriority: this.defaultValues.selectedPriority,
        isComplete: this.defaultValues.isComplete,
      },
    };
  },
  methods: {
    handleSubmit() {
      if (this.validateData()) {
        //clear errors on input boxes
        //  this.titleEmptyError = false;
        //submit the form
        this.formData.deadline = this.formattedDate(); //set forsmattedDate before submitting
        this.$emit('form-submitted', this.formData);
        this.closeModal();
        //success toastr
        this.emitNotification('success', 'Task Added Successfully');
      }
    },
    //a helper method to validate input data
    validateData() {
      return (
        this.formData.title &&
        this.formData.description &&
        !this.formDataTitles.includes(this.formData.title)
      );
    },
    updateEntry() {
      if (this.formData.description) {
        this.formData.deadline = this.formattedDate(); //set forsmattedDate before submitting
        this.$emit('entry-updated', this.editingIndex, this.formData);
        this.closeModal();
        //success toastr
        this.emitNotification('success', 'Task Updated Successfully');
      }
    },
    closeModal() {
      this.$emit('close');
    },
    formattedDate() {
      if (!this.formData.nonFormattedDeadline) {
        return '';
      }
      return moment(this.formData.nonFormattedDeadline).format('MM/DD/YY');
    },
    emitNotification(type, message) {
      this.$emit('show-notification', type, message);
    },
  },
};
</script>
