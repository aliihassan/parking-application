<template>
  <div>
    <h2>Available parking spots: {{ availableParkingSpots }}</h2>
    <span v-if="parkingFull">PARKING FULL!</span>
    <div v-else>
      <button @click="ticketWasSelected()">Take Ticket</button>
    </div>
    <hr v-if="customers.length !== 0" />
    <p v-for="customer in customers" :key="customer.id">Ticket {{ customer.id }} was purchased.</p>
    <hr />
    <h1>Please enter your ticket number to pay before exiting:</h1>
    <p>
      <input type="text" v-model="ticketNumber" :disabled="customers.length === 0" />
    </p>
    <button @click="ticketWasPaid()" :disabled="customers.length === 0">Pay Ticket</button>
    <div v-if="selectedTicket && selectedTicket !== ''">
      <p>Time of purchase: {{ selectedTicket.timeOfPurchase.format('DD/MM/YYYY hh:mm:ss') }}</p>
      <p>Rate: ${{ rate }}</p>
      <p>Amount due: ${{ amountDue }}</p>
    </div>
  </div>
</template>

<script>
import * as moment from "moment";

export default {
  name: "app",
  data() {
    return {
      counter: 1,
      parkingFull: false,
      availableParkingSpots: 5,
      customers: [],
      ticketNumber: "",
      selectedTicket: "",
      amountDue: "",
      rate: ""
    };
  },
  mounted() {
    this.parkingFull = this.isParkingFull(this.availableParkingSpots);
  },
  methods: {
    ticketWasSelected() {
      this.customers.push({
        id: this.counter++,
        timeOfPurchase: moment().subtract(this.getRandomHoursParked(), "hours"),
        ticketPaid: false,
        timeOfExit: ""
      });

      this.availableParkingSpots--;
      this.parkingFull = this.isParkingFull(this.availableParkingSpots);
    },
    ticketWasPaid() {
      // retrieve the selected ticket to be paid
      this.selectedTicket = this.customers.find(customer => {
        return customer.id === parseInt(this.ticketNumber);
      });

      if (this.selectedTicket) {
        this.customers = this.customers.filter(customer => {
          return customer.id !== parseInt(this.ticketNumber);
        });

        const hours = this.totalHoursParked();

        this.amountDue = this.calculateTotalAmount(hours);
        this.availableParkingSpots++;
        this.parkingFull = this.isParkingFull(this.availableParkingSpots);
        this.selectedTicket.ticketPaid = true;
      } else {
        alert("You have already paid this ticket.");
      }
    },
    isParkingFull(availableParkingSpots) {
      return this.availableParkingSpots === 0 ? true : false;
    },
    totalHoursParked() {
      let currentTime = moment();
      let ms = currentTime.diff(this.selectedTicket.timeOfPurchase);
      let d = moment.duration(ms);
      let hours = Math.round(d.as("hours"));

      return hours;
    },
    calculateTotalAmount(hours) {
      return hours * this.getRate(hours);
    },
    getRate(hours) {
      /*
        Rates are set between the following times:
        x < 3: $3/hr
        3 <= x < 6: $4.50/hr
        6 <= x < 9: $6.75/hr
        9 <= x: $10.125/hr = ALL DAY
      */
      let currentRate = 3;

      if (hours < 3) {
        currentRate = 3;
      } else if (hours >= 3 && hours < 6) {
        currentRate = 4.5;
      } else if (hours >= 6 && hours < 9) {
        currentRate = 6.75;
      } else if (hours >= 9) {
        currentRate = 10.125;
      }

      this.rate = currentRate;

      return currentRate;
    },
    getRandomHoursParked() {
      return Math.floor(Math.random() * 10);
    }
  }
};
</script>

<style>
</style>
