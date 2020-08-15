<template>
  <div class="home">
    <div class="table-location">
      <b-form>
        <b-form-group id="query-search-group" label-for="query-search">
          <b-form-input
            id="query-search"
            v-model="query"
            required
            placeholder="Search Crowd Information by Location or Sub Location"
          ></b-form-input>
        </b-form-group>
      </b-form>
      <b-table
        striped
        hover
        :items="computedLocations"
        :fields="fields"
        :busy="isBusy"
        show-empty
        responsive
        class="text-left"
      >
        <template v-slot:cell(is_crowded)="data">
          <b v-if="data.value" class="text-danger">
            <b-badge variant="danger">HIGH</b-badge></b
          >
          <b v-else class="text-success">
            <b-badge variant="success">LOW</b-badge></b
          >
        </template>
        <template v-slot:cell(status)="data">
          <b v-if="data.value === 'active'" class="text-danger">
            <b-badge variant="primary">ACTIVE</b-badge></b
          >
          <b v-else class="text-success">
            <b-badge variant="secondary">INACTIVE</b-badge></b
          >
        </template>
        
        <template v-slot:table-busy>
          <div class="text-center my-2">
            <b-spinner
              variant="primary"
              label="Spinning"
              class="align-middle"
            ></b-spinner>
          </div>
        </template>
      </b-table>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import LocationCard from "@/components/LocationCard.vue";
import axios from "axios";

export default {
  name: "Home",
  components: {},
  data() {
    return {
      isBusy: true,
      query: "",
      fields: [
        { key: "location_name", label: "Location" },
        { key: "sublocation_name", label: "Sub Location" },
        { key: "is_crowded", label: "Crowd Density" },
        "last_update",
      ],
      locations: [], // list of locations, each location contains sublocations and its most recent crowd density
    };
  },
  created() {
    axios
      .get("http://localhost:5000/get-crowd-density-information")
      .then((res) => {
        const data = res.data.data;
        const keys = Object.keys(data);
        keys.forEach((key) => {
          const location_name = key;
          const location_id = data[key].id;
          const sublocations = data[key].sublocations;
          sublocations.forEach((sublocation) => {
            this.locations.push({
              location_name: location_name,
              sublocation_name: sublocation.sublocation_name,
              is_crowded: sublocation.is_crowded,
              last_update: sublocation.created_at,
              status: sublocation.status,
            });
          });
        });
      })
      .catch((e) => {
        alert(String(e));
        this.locations = [];
      })
      .finally(() => {
        this.toggleBusy();
      });
  },
  computed: {
    computedLocations() {
      return this.locations.filter((loc) => {
        return (
          loc.location_name.toLowerCase().includes(this.query.toLowerCase()) ||
          loc.sublocation_name.toLowerCase().includes(this.query)
        );
      });
    },
  },
  methods: {
    toggleBusy() {
      this.isBusy = !this.isBusy;
    },
  },
};
</script>

<style scoped>
.table-location {
  margin-left: 30px;
  margin-right: 30px;
}
</style>
