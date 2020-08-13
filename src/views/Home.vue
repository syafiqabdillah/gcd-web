<template>
  <div class="home">
    <div class="table-location">
      <b-form>
        <b-form-group
          id="query-search-group"
          label-for="query-search"
        >
          <b-form-input
            id="query-search"
            v-model="query"
            required
            placeholder="Search Location or Sublocation"
          ></b-form-input>
        </b-form-group>
      </b-form>
      <b-table striped hover :items="computedLocations" :fields="fields"> </b-table>
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
      query: "",
      fields: [
        "location_name",
        "sublocation_name",
        "is_crowded",
        "last_update",
      ],
      locations: [], // list of locations, each location contains sublocations and its most recent crowd density
    };
  },
  created() {
    axios
      .get("http://localhost:5000/get-locations")
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
            });
          });
        });
      })
      .catch((e) => {
        console.log(String(e));
      });
  },
  computed: {
    computedLocations() {
      return this.locations.filter(loc => {
        console.log()
        return loc.location_name.toLowerCase().includes(this.query.toLowerCase()) 
        || loc.sublocation_name.toLowerCase().includes(this.query)
      })
    }
  }
};
</script>

<style scoped>
.table-location {
  margin-left: 30px;
  margin-right: 30px;
}
</style>
