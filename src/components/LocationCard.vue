<template>
<div>
    <b-card class="location-card" no-body>
        <div class="row align-items-center" style="height: 110px;">
            <div class="col">
                <img v-if="locationName == 'Gramedia Depok'" src="@/assets/gramedia.png" alt="Gramedia" style="max-height: 80px" class="img-fluid" />
                <img v-else-if="locationName == 'Margocity'" src="@/assets/margo.jpg" alt="Margo" style="max-height: 80px" class="img-fluid" />
                <img v-else src="@/assets/gcd.png" alt="GCD" style="max-height: 80px" class="img-fluid" />

            </div>

            <div class="col-5">

                <h3 class="row">{{locationName}}</h3>
                <h5 class="row">{{sublocationName}}</h5>

            </div>

            <div class="col-4" style="height: 110px">

                <div class="crowd-label-red" v-if="isCrowded">
                    <div class="center font-white">
                        <img src="@/assets/high.png" alt="high" class="img-fluid" style="max-height: 18px">
                        <b style="margin-left: 10px">HIGH</b>
                    </div>
                </div>
                <div class="crowd-label-green" v-else>
                    <div class="center font-white">
                        <img src="@/assets/low.png" alt="low" class="img-fluid" style="max-height: 18px">
                        <b style="margin-left: 10px">LOW</b>
                    </div>
                </div>

                <div class="center-paragraph">
                    <div style="position: relative; height: 66px; width: 100%;">

                        <div class="center"><b>Last Update:</b><br>{{lastUpdate | moment("dddd, Do MMMM YYYY | h:mm:ss a")}}</div>
                    </div>

                </div>

            </div>

        </div>

    </b-card>
</div>
</template>

<script>
export default {
    props: ['locationName', 'sublocationName', "isCrowded", "lastUpdate"],
    name: 'LocationCard'
}
</script>

<style scoped>

.location-card {
    border-color: #CED4DA; 
    border-width: 1.5px; 
    overflow: hidden;

}

.center-paragraph {
    height: 66px;
    position: absolute;
    bottom: 0%;
    right: 0px;
    width: 100%;
}

.center {
    margin: 0;
    position: absolute;
    top: 50%;
    left: 50%;
    -ms-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
    font-size: 12px;
}

.font-white {
    color: white;
    font-size: 18px;
}

h3 {
    font-size: 27px;
}

h5 {
    font-size: 17px;
}

.crowd-label-red {
    position: absolute; 
    top: 0px; right: 0px; 
    height: 40%; width: 100%; 
    background-color: #DC3545; 
    border-radius: 0px 0px 0px 50px;
}

.crowd-label-green {
    position: absolute; 
    top: 0px; right: 0px; 
    height: 40%; width: 100%; 
    background-color: #1BC47D; 
    border-radius: 0px 0px 0px 50px;
}

@media screen and (max-width: 770px) {
    h3 {
        font-size: 21px;
    }

    h5 {
        font-size: 13px;
    }

    .center {
        margin: 0;
        position: absolute;
        top: 50%;
        left: 50%;
        -ms-transform: translate(-50%, -50%);
        transform: translate(-50%, -50%);
        font-size: 9.5px;
    }

    .font-white {
        color: white;
        font-size: 15px;
    }
}
</style>
