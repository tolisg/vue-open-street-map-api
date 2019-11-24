<template>
  <div class="container-fluid">
    <div class="loader" v-if="loading">
      <img src="https://icon-library.net/images/spinner-icon-gif/spinner-icon-gif-10.jpg" />
    </div>
    <div class="row" v-show="!loading">
      <div class="col-sm-12">
        <h1 class="text-center">Openstreet Map API</h1>
        <div class="row mb-2">
          <div class="col-sm-12">
            <div id="map" style="height:300px; border: 2px solid black;"></div>
          </div>
        </div>
        <div class="row">
          <div class="col-sm-12 form-group">
            <label>Please search an address</label>
            <input
              class="form-control"
              type="text"
              v-model="address"
              @keyup="get_addresses_debounced"
            />
          </div>
        </div>
        <AddressList
          :address_list="adresses"
          v-if="adresses.length > 0"
          @selectAdress="selectAdress"
        ></AddressList>
        <selectAdress :selected_address="selectedAddress" v-if="selectedAddress"></selectAdress>
      </div>
    </div>
  </div>
</template>

<script>
import _ from "lodash";
import L from "leaflet";
import axios from "axios";
import "leaflet/dist/leaflet.css";
import AddressList from "@/components/AddressList.vue";
import selectAdress from "@/components/SelectedAddress.vue";
export default {
  name: "MapView",
  components: {
    AddressList,
    selectAdress
  },
  data() {
    return {
      map: null,
      tileLayer: null,
      address: null,
      slc_address: {
        LAT: 37.9908997,
        LON: 23.70332
      },
      adresses: [],
      selectedAddress: null,
      loading: false,
      get_addresses_debounced: null
    };
  },
  created() {
    this.get_addresses_debounced = _.debounce(this.get_addresses, 1000);
  },
  mounted() {
    this.initMap();
  },
  methods: {
    initMap() {
      this.map = L.map("map").setView(
        [this.slc_address.LAT, this.slc_address.LON],
        16
      );
      this.tileLayer = L.tileLayer(
        "https://cartodb-basemaps-{s}.global.ssl.fastly.net/rastertiles/voyager/{z}/{x}/{y}.png",
        {
          maxZoom: 18,
          attribution:
            '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>, &copy; <a href="https://carto.com/attribution">CARTO</a>'
        }
      );

      this.tileLayer.addTo(this.map);
    },
    async get_addresses() {
      this.loading = true;
      let response = [];
      try {
        // Comment this line and uncomment the next one to make local api calls
        response = await axios.get`https://nominatim.openstreetmap.org/search?q=${this.address}&format=json&addressdetails=1`;
        // response = await axios.get("./index.json");
      } catch (error) {
        console.error(error);
      }
      this.adresses = response.data;
      this.loading = false;
    },
    selectAdress(address) {
      this.selectedAddress = address;
      this.initMarkder(address);
      this.map.setView(
        [this.selectedAddress.lat, this.selectedAddress.lon],
        16
      );
    },
    initMarkder(address) {
      L.marker([address.lat, address.lon], {
        icon: L.icon({
          iconUrl: "https://unpkg.com/leaflet@1.5.1/dist/images/marker-icon.png"
        })
      }).addTo(this.map);
    }
  }
};
</script>
<style scoped>
.loader {
  position: absolute;
  z-index: 9999;
  background: white;
  width: 99vw;
  text-align: center;
}
</style>
