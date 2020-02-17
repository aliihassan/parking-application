<template>
    <div>
        <h2>Available parking spots: {{ availableParkingSpots }}</h2>
        {{ timeOfPurchase }}
        <span v-if="parkingFull">PARKING FULL!</span>
        <div v-else>
            <button @click="ticketWasSelected()">Take Ticket</button>
        </div>
    </div>
</template>

<script>
import * as moment from 'moment';

export default {
    data() {
        return {
            parkingFull: false,
            availableParkingSpots: 10,
            ticketPaid: false,
            timeOfPurchase: null,
        }
    },
    mounted() {
        this.parkingFull = this.availableParkingSpots === 0 ? true : false;
    },
    methods: {
        ticketWasSelected: function() {
            this.timeOfPurchase = moment().format('YYYY/DD/MM hh:mm:ss'); 
            this.availableParkingSpots -= 1;

            if (this.availableParkingSpots == 0) {
                this.parkingFull = true;
            }
        }
    }
}
</script>