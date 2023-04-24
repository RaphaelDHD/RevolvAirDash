<template>
    <div>
        <img :src="srcWind" style="height: 350px; width: 350px">
        <button @click="changeDates">Change Dates</button>
    </div>
</template>

<script>
import {computed, ref} from "vue";

const baseURLWind = ref(`https://mesonet.agron.iastate.edu/cgi-bin/mywindrose.py?`);
const srcWind = ref("")

export default {
    name: "ChartsComponents",
    computed: {
        srcWind(){
            return srcWind.value
        }
    },
    data() {
        return {
            nsector: 36,
            station: 'CXBO',
            network: 'CA_QC_ASOS',
            day1: 16,
            day2: 18,
            month1: 4,
            month2: 4,
            year1: 2023,
            year2: 2023,
            hour1: 20,
            hour2: 23
        }
    },
    methods: {
        changeDates() {
            this.day1--;
            this.day2++;
            this.updateSrcWind();
        },
        updateSrcWind() {
            srcWind.value = `${baseURLWind.value}nsector=${this.nsector}&station=${this.station}&network=${this.network}&day1=${this.day1}&day2=${this.day2}&month1=${this.month1}&month2=${this.month2}&year1=${this.year1}&year2=${this.year2}&hour1=${this.hour1}&hour2=${this.hour2}&minute1=0&minute2=0&units=mph&fmt=png&dpi=100&conv=from`;
        }
    },
    mounted() {
        this.updateSrcWind();
    }
}
</script>

<style scoped>
</style>
