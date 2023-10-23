<template>
  <Modal ref="modalRef">
    <template #header>
      <h2>Broneeri reis</h2>
    </template>
    <template #body>
      <h3>{{ `${fromCityName} - ${toCityName}` }}</h3>
      <h4>{{ getTimeFromTimestamp() }}</h4>
      <h4>{{ `${schedule.price}€, ${schedule.companyName}` }}</h4>
      <table>
        <tbody>
        <tr>
          <td><label for="firstName">Eesnimi<sup style="color: red">*</sup></label></td>
          <td><input v-model="bookingRequest.firstName" type="text" id="firstName"></td>
        </tr>
        <tr>
          <td><label for="lastName">Perekonnanimi<sup style="color: red">*</sup></label></td>
          <td><input v-model="bookingRequest.lastName" type="text" id="lastName"></td>
        </tr>
        <tr>
          <td><label for="email">Email</label></td>
          <td><input type="text" id="email"></td>
        </tr>
        </tbody>
      </table>
    </template>
    <template #footer>
      <div class="my-modal-footer">
        <button @click="closeModal" class="btn btn-dark">Tagasi</button>
        <button @click="addBooking" class="btn btn-dark">Kinnita</button>
      </div>
    </template>

  </Modal>

</template>

<script>
import Modal from "@/components/modal/Modal.vue";
import router from "@/router";

export default {
  name: 'BookingModal',
  components: {Modal},
  data() {
    return {
      fromCityName: '',
      toCityName: '',
      schedule: {
        id: 0,
        startTimeDateTime: '',
        endTimeDateTime: '',
        companyName: '',
        price: 0
      },
      bookingRequest: {
        scheduleId: '',
        firstName: '',
        lastName: ''
      }

    }
  },
  methods: {
    getTimeFromTimestamp() {
      let fromDate = new Date(this.schedule.startTimeDateTime);
      let endDate = new Date(this.schedule.endTimeDateTime);
      return `${fromDate.getUTCHours()}:${fromDate.getUTCMinutes()}-${endDate.getUTCHours()}:${endDate.getUTCMinutes()}`
    },

    closeModal() {
      this.$refs.modalRef.closeModal()
    },

    addBooking() {
      this.setBookingRequestScheduleId();
      this.$http.post("/booking", this.bookingRequest
      ).then(response => {
        const responseBody = response.data
        this.closeModal()
        this.$emit("event-booking-added")
      }).catch(error => {
        router.push({name: 'errorRoute'})
      })
    },

    setBookingRequestScheduleId() {
      this.bookingRequest.scheduleId = this.schedule.id
    }
  }
}
</script>
