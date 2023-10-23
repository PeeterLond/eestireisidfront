<template>
  <div class="home-master">
    <div>
      <h2>Tere tulemast Reisi Eestis kodulehele.</h2>
      <AlertDanger :alert-message="alertMessage"></AlertDanger>
    </div>
    <div class="home-item">
      <label class="me-3" for="from">Lähtekoht</label>
      <input v-model="from" class="me-5" type="text" id="from">
      <label class="me-3" for="to">Sihtkoht</label>
      <input v-model="to" class="me-5" type="text" id="to">
      <button @click="handleSearch" class="btn btn-dark">Otsi reise</button>
    </div>

  </div>
</template>

<script>

import router from "@/router";
import AlertDanger from "@/components/alert/AlertDanger.vue";

export default {
  name: 'HomeView',
  components: {AlertDanger},
  data() {
    return {
      from: '',
      to: '',
      alertMessage: ''
    }
  },
  methods: {
    handleSearch() {
      this.resetErrorMessage();
      if (!this.mandatoryFieldsAreFilled()) {
        this.alertMessage = 'Täida kõik väljad'
      }
      else {
        this.goToSearchView()
      }
    },

    resetErrorMessage() {
      this.alertMessage = ''
    },

    mandatoryFieldsAreFilled() {
      return this.from.length > 0 && this.to.length > 0
    },

    goToSearchView() {
      router.push({name: 'searchRoute', query: {from: this.from, to: this.to}})
    },

  }
}
</script>
