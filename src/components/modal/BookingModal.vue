<template>
  <Modal ref="modalRef">
    <template #header>
      <h2>Broneeri reis</h2>
    </template>
    <template #body>
      <AlertDanger :alert-message="errorResponse.message"></AlertDanger>
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
        <button @click="handleBooking" class="btn btn-dark">Kinnita</button>
      </div>
    </template>
  </Modal>
</template>

<script>
import Modal from "@/components/modal/Modal.vue";
import router from "@/router";
import AlertDanger from "@/components/alert/AlertDanger.vue";

export default {
  name: 'BookingModal',
  components: {AlertDanger, Modal},
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
      },
      errorResponse: {
        message: '',
        errorCode: 0
      }
    }
  },
  methods: {
    getTimeFromTimestamp() {
      let fromDate = new Date(this.schedule.startTimeDateTime);
      let endDate = new Date(this.schedule.endTimeDateTime);
      return `${fromDate.getUTCHours()}:${fromDate.getUTCMinutes()}-${endDate.getUTCHours()}:${endDate.getUTCMinutes()}`;
    },

    closeModal() {
      this.$refs.modalRef.closeModal();
    },

    handleBooking() {
      this.resetErrorMessage();
      if (!this.mandatoryFieldsFilled()) {
        this.errorResponse.message = 'Täida kohustuslikud väljad';
      }
      else {
        this.addBooking();
      }
    },

    resetErrorMessage() {
      this.errorResponse.message = '';
    },

    mandatoryFieldsFilled() {
      return this.bookingRequest.firstName.length > 0 && this.bookingRequest.lastName.length > 0;
    },

    addBooking() {
      this.setBookingRequestScheduleId();
      this.$http.post("/booking", this.bookingRequest
      ).then(response => {
        const responseBody = response.data;
        this.closeModal();
        this.$emit("event-booking-added");
      }).catch(error => {
        this.errorResponse = error.response.data;
        if (this.errorResponse.errorCode !== 222) {
          router.push({name: 'errorRoute'});
        }
      })
    },

    setBookingRequestScheduleId() {
      this.bookingRequest.scheduleId = this.schedule.id
    }
  }
}
</script>
