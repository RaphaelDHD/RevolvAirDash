<script lang="ts">
import { LMap, LTileLayer, LWmsTileLayer, LMarker , LPopup, LControlLayers} from '@vue-leaflet/vue-leaflet'
import 'leaflet/dist/leaflet.css'
import {computed, onMounted, ref} from 'vue'

const stations = ref([]);
const userLong = ref(-71.224028);
const userLat = ref(46.824853);
const userPos = ref([userLong.value , userLat.value]);

function calcCrow({lat1, lon1}: { lat1: any, lon1: any }) {
    var R = 6371; // km
    var dLat = toRad({Value: userLat.value - lat1});
    var dLon = toRad({Value: userLong.value - lon1});
    var thisLat1 = toRad({Value: lat1});
    var lat2 = toRad({Value: userLat.value});

    var a = Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.sin(dLon / 2) * Math.sin(dLon / 2) * Math.cos(thisLat1) * Math.cos(lat2);
    var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    var d = R * c;
    return d;
}

function toRad({Value}: { Value: any }) {
    return Value * Math.PI / 180;
}

async function fetchStations(){
    try {
        const response = await fetch("https://app.revolvair.org/api/revolvair/stations");
        const data = await response.json();
        stations.value = data.data;
    }
    catch(e){
        console.log(e);
    }
}
fetchStations()

export default ({
    components: {
        LMap,
        LTileLayer,
        LWmsTileLayer,
        LMarker,
        LControlLayers
    },
    computed: {
        stations() {
            return stations.value;
        }
    },
    data(){

    }
})

</script>


<template>
  <l-map :zoom="3" :center="[57, -97]" style="height: 600px; width: 700px">
      <l-tile-layer
              :url="'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png'"
              :attribution="'&copy; <a href=\'https://www.openstreetmap.org/copyright\'>OpenStreetMap</a> contributors'"
              :maxZoom="19"
      >
      </l-tile-layer>

      <l-control-layers></l-control-layers>

      <l-wms-tile-layer
          layer-type="overlay"
          :name="'Wind'"
          :url="'https://geo.weather.gc.ca/geomet/'"
          :layers="'HRDPS.CONTINENTAL_UU'"
          :tiled="true"
          :transparent="false"
          :visible="false"
          :opacity="0.5"
          :format="'image/png'"
      >
      </l-wms-tile-layer>
      <!--<l-wms-tile-layer
              :url="'https://cartes.inspq.qc.ca/mapserver/general/canopee.map/'"
              :layers="'canopee_rmr_quebec_2022'"
              :tiled="true"
              :version="'1.3.0'"
              :format="'image/png'"
              :request="'GetMap'"
              :transparent="true"
              :height="1080"
              :width="1920"
              :styles="'Toute la canopée'"
      >
      </l-wms-tile-layer> -->
      <l-wms-tile-layer
          layer-type="overlay"
          :name="'PM2.5'"
          :url="'https://geo.weather.gc.ca/geomet/'"
          :layers="'RAQDPS-FW.SFC_PM2.5'"
          :tiled="true"
          :visible="false"
          :transparent="false"
          :opacity="0.5"
          :format="'image/png'"
      >
      </l-wms-tile-layer>


      <l-marker
              v-for="station in stations"
              :key="station.id"
              :lat-lng="[station.lat, station.long]"
      ></l-marker>
  </l-map>
</template>

