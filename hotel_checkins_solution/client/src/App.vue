<template>
  <div id="app">
    <booking-form/>
    <booking-list :bookings="bookings"/>
  </div>
</template>

<script>
import BookingForm from "@/components/BookingForm";
import BookingList from "@/components/BookingList";
import BookingService from "@/services/BookingService";
import { eventBus } from '@/main';

export default {
  name: 'app',
  components: {
    'booking-form': BookingForm,
    'booking-list': BookingList
  },
  data(){
    return {
      bookings: []
    }
  },
  mounted(){
    eventBus.$on('submit-booking', (booking) => {
      BookingService.addBooking(booking)
        .then(bookingWithId => this.bookings.push(bookingWithId))
    });

    eventBus.$on('booking-delete-clicked', (id) => {
      BookingService.deleteBooking(id)
      const index = this.bookings.findIndex(booking => booking._id === id);
      this.bookings.splice(index, 1);
    });

    eventBus.$on('booking-change', bookingToChange => {
      const updatedBooking = {...bookingToChange, checked_in: !bookingToChange.checked_in}
      BookingService.updateBooking(updatedBooking)
      const index = this.bookings.findIndex(booking => booking._id === bookingToChange._id);
      this.bookings.splice(index, 1, updatedBooking);
    })

    BookingService.getBookings()
    .then(bookings => this.bookings = bookings);
  }
}
</script>

<style>
  body {
    background: #eee;
    font-family: 'Open Sans', sans-serif;
  }

  #app {
    width: 75%;
    margin: 0 auto;
    background: #fff;
    padding: 20px 40px 40px 40px;
  }
</style>
