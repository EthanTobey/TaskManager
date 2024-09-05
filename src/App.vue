<script setup>
import TableHeading from './components/TableHeading.vue';
import AddPopup from './components/AddPopup.vue';
import toastr from 'toastr';
</script>

<template>
  <div id="app">
    <TableHeading @show-notification="showNotification" />
  </div>
</template>

<script>
toastr.options = {
  closeButton: true,
  debug: false,
  newestOnTop: false,
  progressBar: false,
  positionClass: 'toast-bottom-right',
  preventDuplicates: false,
  onclick: null,
  showDuration: '300',
  hideDuration: '1000',
  timeOut: '3000',
  extendedTimeOut: '1000',
  showEasing: 'swing',
  hideEasing: 'linear',
  showMethod: 'fadeIn',
  hideMethod: 'fadeOut',
};
export default {
  components: {
    TableHeading,
  },
  methods: {
    showNotification(type, message) {
      toastr[type](message);
    },
  },
};
</script>
