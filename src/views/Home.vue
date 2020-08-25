<template>
<div class="container-sm home display">

    <div class="row d-flex flex-row justify-content-center">
        <div class="d-flex width-location">
            <b-form style="width: 100%;">
                <b-form-group id="query-search-group" label-for="query-search">
                    <b-form-input 
                        id="query-search" 
                        v-model="query" 
                        required 
                        placeholder="Search Crowd Information by Place Name or Venue Name"
                    ></b-form-input>
                </b-form-group>
            </b-form>
        </div>
    </div>

    <div class="row d-flex flex-row justify-content-center mb-3">
        <div class="d-flex width-location">
            <div class="col d-flex flex-row justify-content-center">
                <p style="margin:0;" class="p-1">Sort By</p> 
            </div>
            <div class="col d-flex flex-row justify-content-center">
                <input class="w-100 ml-1 mr-1 p-1 btn btn-outline-secondary" type="button" value="Place">
            </div>
            <div class="col">
                <input class="w-100 ml-1 mr-1 p-1 btn btn-outline-secondary" type="button" value="Venue">
            </div>
            <div class="col">
                <input class="w-100 ml-1 mr-1 p-1 btn btn-outline-secondary" type="button" value="Time Update">
            </div>
            <div class="col">
                <input class="w-100 ml-1 mr-1 p-1 btn btn-outline-secondary" type="button" value="Crowd Density">
            </div>

        </div>
        
    </div>



    <div class="row d-flex flex-row justify-content-center">

        <div class="width-location" v-if="!isBusy">
            <div v-for="(item, index) in computedLocations" :key="index" style="width:100%; margin-bottom: 8px">
                <location-card 
                    :locationName="item.location_name" 
                    :sublocationName="item.sublocation_name" 
                    :isCrowded="item.is_crowded" 
                    :lastUpdate="item.last_update" 
                />
            </div>

        </div>
        
        <div v-else>
            <b-spinner
                style="width: 5rem; height: 5rem; color: #4F59B0; margin-top: 60px; border-width: 10px"
            ></b-spinner>

        </div>
    </div>
</div>
</template>

<script>
// @ is an alias to /src
import LocationCard from "@/components/LocationCard.vue";
import axios from "axios";

export default {
    name: "Home",
    components: {
        LocationCard,
    },
    data() {
        return {
            isBusy: true,
            query: "",
            fields: [{
                    key: "location_name",
                    label: "Location"
                },
                {
                    key: "sublocation_name",
                    label: "Sub Location"
                },
                {
                    key: "is_crowded",
                    label: "Crowd Density"
                },
                "last_update",
            ],
            locations: [], // list of locations, each location contains sublocations and its most recent crowd density
        };
    },
    created() {
        axios
            .get("http://gcd-api.herokuapp.com/get-crowd-density-information")
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

.width-location {
    width: 70%;
}

.btn-outline-secondary {
    background-color: white;
    border-color: #CED4DA;
    font-size: 15px;
}

@media screen and (max-width: 600px) {
    .width-location {
        width: 100%;
    }
 
    .btn-outline-secondary {
        background-color: white;
        border-color: #CED4DA;
        font-size: 12px;
    }
    
    .ml-1 {
        margin-left: 0;
    }

    .mr-1 {
        margin-right: 0;
    }
}
</style>
