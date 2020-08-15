<template>
  <div class="suggest-location text-left">
    <div align="center">
      <h3>Suggest New Location</h3>
    </div>

    <b-form @submit="onSubmit" class="suggestion-form">
      <b-form-group
        id="input-suggestion-form-1"
        label="Location name:"
        label-for="location-name-form"
      >
        <b-form-input
          id="location-name-form"
          v-model="form.location_name"
          required
          placeholder="Enter location name"
        ></b-form-input>
      </b-form-group>

      <b-form-group
        id="input-suggestion-form-2"
        label="Sublocation name:"
        label-for="sublocation-name-form"
      >
        <b-form-input
          id="sublocation-name-form"
          v-model="form.sublocation_names"
          required
          placeholder="Enter sublocation names, separated by comma"
        ></b-form-input>
        <b-form-text>e.g. Lobby, South gate, Basement</b-form-text>
      </b-form-group>

      <b-button type="submit" variant="primary">
        <b-spinner type="grow" small v-if="isAddingLocation"></b-spinner>
        {{ isAddingLocation ? "Submitting" : "Submit" }}
      </b-button>
    </b-form>

    <div id="location-table">
      <b-table
        striped
        hover
        show-empty
        :items="computedSuggestedLocation"
        :fields="fields"
        :busy="isBusy"
        responsive
        class="text-left"
      >
        <template v-slot:cell(is_active)="data">
          <p v-if="data.value">
            Yes
          </p>
          <p v-else>No</p>
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
import axios from "axios";

export default {
  name: "SuggestLocation",
  data() {
    return {
      form: {
        location_name: "",
        sublocation_names: "",
      },
      locations: [],
      fields: [
        { key: "location_name", label: "Location" },
        { key: "sublocation_name", label: "Sublocation" },
        { key: "sublocation_status", label: "Status" },
      ],
      isBusy: true,
      isAddingLocation: false,
    };
  },
  created() {
    axios
      .get("http://localhost:5000/get-suggested-locations")
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
              sublocation_status: sublocation.sublocation_status,
            });
          });
        });
        // this.locations = data;
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
    computedSuggestedLocation() {
      return this.locations.filter((loc) => {
        return true;
      });
    },
  },
  methods: {
    onSubmit(event) {
      event.preventDefault();
      if (this.form.location_name !== "") {
        this.isAddingLocation = true;
        axios
          .post("http://localhost:5000/suggest-location", {
            location_name: this.form.location_name,
            sublocation_names: this.form.sublocation_names,
          })
          .then(() => {
            alert("Successfully added location");
            this.locations.push(this.form.location_name);
            this.form.location_name = "";
          })
          .catch((err) => {
            alert(String(err));
            // alert("Adding location failed, try again later.");
          })
          .finally(() => {
            this.isAddingLocation = false;
            location.reload();
          });
      }
    },
    toggleBusy() {
      this.isBusy = !this.isBusy;
    },
  },
};
</script>

<style scoped>
.suggestion-form {
  margin-top: 30px;
  margin-left: 30px;
  margin-right: 30px;
}
#location-table {
  margin-top: 30px;
  margin-left: 30px;
  margin-right: 30px;
}
</style>
