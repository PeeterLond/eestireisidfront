<template>
  <BookingModal @event-booking-added="updateSuccessMessage" ref="bookingModalRef"></BookingModal>
  <div class="search-master">
    <div class="filter-item">
      <p>Filter1</p>
      <p>Filter2</p>
      <p>Filter3</p>
      <p>Filter4</p>
      <button class="btn btn-dark">Filtreeri</button>
    </div>
    <div class="search-item">
      <div>
        <label class="me-3" for="from">Lähtekoht</label>
        <input v-model="fromCity" class="me-5" type="text" id="from">
        <label class="me-3" for="to">Sihtkoht</label>
        <input class="me-5" v-model="toCity" type="text" id="to">
        <button @click="handleSearch" class="btn btn-dark">Otsi reise</button>
      </div>
      <div class="mt-5">
        <AlertDanger :alert-message="errorResponse.message"></AlertDanger>
        <AlertSuccess :alert-message="successMessage"></AlertSuccess>
        <table class="table table-bordered table-hover">
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
import {useRoute} from "vue-router";

export default {
  name: "SearchView",
  components: {AlertSuccess, BookingModal, AlertDanger},
  data() {
    return {
      fromCity: '',
      toCity: '',
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

    handleSearch() {
      this.resetAlertMessages();
      if (!this.mandatoryFieldsAreFilled()) {
        this.errorResponse.message = 'Täida kõik väljad'
      } else {
        this.searchSchedule()
      }
    },

    resetAlertMessages() {
      this.errorResponse.message = '';
      this.successMessage = '';
    },

    mandatoryFieldsAreFilled() {
      return this.fromCity.length > 0 && this.toCity.length > 0
    },

    searchSchedule() {
      this.$http.get("/search", {
            params: {
              fromCity: this.fromCity,
              toCity: this.toCity
            }
          }
      ).then(response => {
        this.searchResponse = response.data;
      }).catch(error => {
        this.errorResponse = error.response.data;
        if (this.errorResponse.errorCode !== 111) {
          router.push({name: 'errorRoute'})
        }
      })
    },

    getTimeFromTimestamp(fromTime, endTime) {
      let fromDate = new Date(fromTime);
      let endDate = new Date(endTime);
      return `${fromDate.getUTCHours()}:${fromDate.getUTCMinutes()}-${endDate.getUTCHours()}:${endDate.getUTCMinutes()}`

    },

    openBookingModal(fromCityName, toCityName, schedule) {
      this.$refs.bookingModalRef.$refs.modalRef.openModal();
      this.$refs.bookingModalRef.fromCityName = fromCityName;
      this.$refs.bookingModalRef.toCityName = toCityName;
      this.$refs.bookingModalRef.schedule = schedule;
    },

    checkIfQueryParamsExists() {
      if ((useRoute().query.from).length > 0) {
        this.fromCity = useRoute().query.from;
        this.toCity = useRoute().query.to;
        this.searchSchedule();
      }
    },
  },
  beforeMount() {
    this.checkIfQueryParamsExists();
  }
}
</script>


