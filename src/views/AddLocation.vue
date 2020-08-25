<template>
  <div class="text-left container">
    <div align="center" class="mb-4">
      <h3>
        Register New Location
      </h3>
    </div>

    <div>
      <b-row class="w-100 d-flex flex-row justify-content-center">
        <!-- <b-col></b-col> -->
        <div style="width: 65%">
          <b-card style="padding: 25px 50px 25px 50px;">
            <b-form @submit="onSubmit">
              <b-form-group
                id="input-add-location-form-1"
                label="Location name"
                label-for="location-name-form"
              >
                <b-form-input
                  id="add-location-name-form"
                  v-model="form.location_name"
                  required
                  placeholder="Enter location name"
                ></b-form-input>
              </b-form-group>

              <b-form-group
                id="input-suggestion-form-2"
                label="Sublocation names"
                label-for="sublocation-name-form"
              >
                <b-form-text>e.g. Lobby, South gate, Basement</b-form-text>
                <b-form-input
                  id="sublocation-name-form"
                  v-model="form.sublocation_names"
                  required
                  placeholder="Enter sublocation names, separated by comma"
                ></b-form-input>
                
              </b-form-group>

              <div class="d-flex flex-row justify-content-center pt-3">
                <b-button block type="submit" style="width: 50%; background-color: #4F59B0; border-width: 0px; padding: 10px;">
                <b-spinner
                  type="grow"
                  small
                  v-if="isAddingLocation"
                ></b-spinner>
                {{ isAddingLocation ? "Submitting" : "Add Location" }}
              </b-button>

              </div>

              
            </b-form>
          </b-card>
        <hr>
          <b-card>
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
                <template v-slot:cell(sublocation_status)="data">
                  <b-badge v-if="data.value === 'active'" variant="primary"
                    >ACTIVE</b-badge
                  >
                  <b-badge
                    v-else-if="data.value === 'inactive'"
                    variant="secondary"
                    >INACTIVE</b-badge
                  >
                  <b-badge
                    v-else-if="data.value === 'suggested'"
                    variant="warning"
                    >SUGGESTED</b-badge
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
          </b-card>
        </div>
      </b-row>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "AddLocation",
  data() {
    return {
      form: {
        location_name: "",
        sublocation_names: "",
      },
      isAddingLocation: false,
      locations: [],
      fields: [
        { key: "location_name", label: "Location", sortable: true },
        { key: "sublocation_name", label: "Sub Location", sortable: true },
        {
          key: "sublocation_status",
          label: "IoT Device Status",
          sortable: false,
        },
      ],
      isBusy: true,
      query: "",
    };
  },
  created() {
    axios
      .get("http://gcd-api.herokuapp.com/get-all-sublocations")
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
      })
      .catch((e) => {
        alert(String(e));
        this.locations = [];
      })
      .finally(() => {
        this.isBusy = !this.isBusy;
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
          .post("http://localhost:5000/add-location", {
            location_name: this.form.location_name,
            sublocation_names: this.form.sublocation_names,
          })
          .then(() => {
            alert("Successfully added location");
            this.form.location_name = "";
          })
          .catch((err) => {
            alert(String(err));
            // alert("Adding location failed, try again later.");
          })
          .finally(() => {
            this.isAddingLocation = false;
            location.href = "/";
          });
      }
    },
    toggleBusy() {
      this.isBusy = !this.isBusy;
    },
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
};
</script>

<style scoped>
.add-location-form {
  margin-top: 30px;
  margin-left: 30px;
  margin-right: 30px;
}


#location-table {
  margin-top: 30px;
  margin-left: 30px;
  margin-right: 30px;
}
.table-location {
  margin: 30px;
}
.add-location-container {
  margin: 30px;
}
button {
    background-color: #318fb5;
}

input {
  border-width: 0;
  border-bottom-width: 2px;
  margin-bottom: 30px;
}

input:focus {
  border-width: 0;
  border-bottom-width: 2px;
  margin-bottom: 30px;
  box-shadow: 0 1px 1px rgba(81, 203, 238, 1);
  color: black;
}

input:focus::placeholder {
  color: rgba(81, 203, 238, 1);
  
}

label {
  color: black;
}

</style>
