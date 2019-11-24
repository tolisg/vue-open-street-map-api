<template>
  <div class="row mb-3">
    <div class="col-sm-12">
      <h1>You selected the below address:</h1>
      <strong>{{ this.selected_address.display_name }}</strong>
    </div>
    <div class="col-sm-12">{{ selected_address }}</div>
  </div>
</template>

<script>
export default {
  name: "SelectedAddress",
  props: {
    selected_address: {
      default: ""
    }
  }
};
</script>