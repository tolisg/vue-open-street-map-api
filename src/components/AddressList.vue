<template>
  <div class="row">
    <div class="col-sm-12">
      <ul class="list-group">
        <li class="list-group-item" v-for="adress in address_list" :key="adress.place_id">
          <a href="javascript:;" @click="selectAdress(adress)">{{adress.display_name}}</a>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "AddressList",
  props: {
    address_list: {
      required: true,
      default: []
    }
  },
  methods: {
    selectAdress(address) {
      this.$emit("selectAdress", address);
    }
  }
};
</script>