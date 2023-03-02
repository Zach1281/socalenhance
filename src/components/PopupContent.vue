<template>
    <div class="popup-content">
      <h2>{{ title }}</h2>
      <p>{{ description }}</p>
      <button @click="closePopup">Close</button>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      title: String,
      description: String,
    },
    methods: {
      closePopup() {
        this.$emit('close');
      },
    },
  };
  </script>
  
  <style scoped>
  .popup-content {
    max-width: 200px;
  }
  </style>
  