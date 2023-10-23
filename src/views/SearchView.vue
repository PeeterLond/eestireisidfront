<template>
  <BookingModal @event-booking-added="updateSuccessMessage" ref="bookingModalRef"></BookingModal>
  <div class="search-master">
    <div>
      filtreerimine
    </div>
    <div class="search-item">
      <div>
        <label class="me-3" for="from">Lähtekoht</label>
        <input v-model="fromCity" class="me-5" type="text" id="from">
        <label class="me-3" for="to">Sihtkoht</label>
        <input class="me-5" v-model="toCity" type="text" id="to">
        <button @click="searchSchedule" class="btn btn-dark">Otsi reise</button>
      </div>
      <div>
        <AlertDanger :alert-message="errorResponse.message"></AlertDanger>
        <AlertSuccess :alert-message="successMessage"></AlertSuccess>
        <table v-if="scheduleExists" class="table table-bordered">
          <thead>
            <tr>
              <th>Marsuut</th>
              <th>Vedaja</th>
              <th>Aeg</th>
              <th>Hind</th>
              <th>Broneeri</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="schedule in searchResponse.schedules">
              <td>{{searchResponse.fromCityName + '-' + searchResponse.toCityName}}</td>
              <td>{{schedule.companyName}}</td>
              <td>{{getTimeFromTimestamp(schedule.startTimeDateTime, schedule.endTimeDateTime)}}</td>
              <td>{{schedule.price}}€</td>
              <td>
                <button @click="openBookingModal(searchResponse.fromCityName, searchResponse.toCityName, schedule)"
                        class="btn btn-dark">Broneeri</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>


</template>

<script>
import router from "@/router";
import AlertDanger from "@/components/alert/AlertDanger.vue";
import BookingModal from "@/components/modal/BookingModal.vue";
import AlertSuccess from "@/components/alert/AlertSuccess.vue";

export default {
  name: "SearchView",
  components: {AlertSuccess, BookingModal, AlertDanger},
  data() {
    return {
      fromCity: '',
      toCity: '',
      scheduleExists: false,
      successMessage: '',
      searchResponse: {
        fromCityName: '',
        toCityName: '',
        distance: 0,
        schedules: [
          {
            id: 0,
            startTimeDateTime: '',
            endTimeDateTime: '',
            companyName: '',
            price: 0
          }
        ]
      },
      errorResponse: {
        message: '',
        errorCode: 0
      }
    }
  },
  methods: {
    updateSuccessMessage() {
      this.successMessage= 'Reis broneeritud'
    },
    openBookingModal(fromCityName, toCityName, schedule) {
      this.$refs.bookingModalRef.$refs.modalRef.openModal();
      this.$refs.bookingModalRef.fromCityName = fromCityName;
      this.$refs.bookingModalRef.toCityName = toCityName;
      this.$refs.bookingModalRef.schedule = schedule;
    },

    getTimeFromTimestamp(fromTime, endTime) {
      let fromDate = new Date(fromTime);
      let endDate = new Date(endTime);
      return `${fromDate.getUTCHours()}:${fromDate.getUTCMinutes()}-${endDate.getUTCHours()}:${endDate.getUTCMinutes()}`

    },

    searchSchedule() {
      this.resetAlertMessages();
      this.$http.get("/search", {
            params: {
              fromCity: this.fromCity,
              toCity: this.toCity
            }
          }
      ).then(response => {
        this.searchResponse = response.data;
        this.scheduleExists = true;
      }).catch(error => {
        this.errorResponse = error.response.data;
        if (this.errorResponse.errorCode !== 111) {
          router.push({name: 'errorRoute'})
        }
      })
    },

    resetAlertMessages() {
      this.errorResponse.message = '';
      this.successMessage = '';
    },
    checkIfScheduleExists() {
      if (this.searchResponse.length > 0) {
        this.scheduleExists = true;
      }
    }
  },
  beforeMount() {
    this.checkIfScheduleExists();
  }
}
</script>


