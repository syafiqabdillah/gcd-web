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
                <input 
                    class="w-100 ml-1 mr-1 p-1 btn "
                    :class="(sortedBy == 'place')?'btn-outline-secondary-focus':'btn-outline-secondary'" 
                    type="button" 
                    value="Place" 
                    @click="sortedBy = 'place'"
                >
            </div>
            <div class="col">
                <input 
                    class="w-100 ml-1 mr-1 p-1 btn "
                    :class="(sortedBy == 'venue')?'btn-outline-secondary-focus':'btn-outline-secondary'" 
                    type="button" 
                    value="Venue" 
                    @click="sortedBy = 'venue'"
                >
            </div>
            <div class="col">
                <input 
                    class="w-100 ml-1 mr-1 p-1 btn "
                    :class="(sortedBy == 'time')?'btn-outline-secondary-focus':'btn-outline-secondary'" 
                    type="button" 
                    value="Time Update" 
                    @click="sortedBy = 'time'"
                >
            </div>
            <div class="col">
                <input 
                    class="w-100 ml-1 mr-1 p-1 btn "
                    :class="(sortedBy == 'crowd')?'btn-outline-secondary-focus':'btn-outline-secondary'" 
                    type="button" 
                    value="Crowd Density" 
                    @click="sortedBy = 'crowd'"
                >
            </div>

        </div>
        
    </div>

    <div class="row d-flex flex-row justify-content-center">

        <div class="width-location" v-if="!isBusy">
            <div v-for="(item, index) in sortedLocations" :key="index" style="width:100%; margin-bottom: 8px">
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
import moment from 'moment';

export default {
    name: "Home",
    components: {
        LocationCard,
    },
    data() {
        return {
            isBusy: true,
            query: "",
            sortedBy: "",
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
        sortedLocations() {
            if (this.sortedBy == "place"){
                return this.computedLocations.sort((a, b) => (a.location_name.localeCompare(b.location_name)))

            } else if (this.sortedBy == "venue"){
                return this.computedLocations.sort((a, b) => (a.sublocation_name.localeCompare(b.sublocation_name)))

            } else if (this.sortedBy == "time"){
                return this.computedLocations.sort((a, b) => 
                    (moment(a.last_update).isAfter(b.last_update)? 1: -1)
                )

            } else if (this.sortedBy == "crowd"){
                return this.computedLocations.sort((a, b) => 
                    (a.is_crowded - b.is_crowded)
                )
            } else {
                return this.computedLocations
            }
        }
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

.btn-outline-secondary-focus {
    background-color: #4F59B0;
    border-color: #CED4DA;
    color: white;
    font-size: 15px;
}

.btn-outline-secondary:hover {
    background-color: #4F59B0;
    border-color: #CED4DA;
    color: white;
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
