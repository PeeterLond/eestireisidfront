<template>
  <div class="home-master">
    <div>
      <h2>Tere tulemast Reisi Eestis kodulehele.</h2>
    </div>
    <div>
      <label class="me-3" for="from">Lähtekoht</label>
      <input v-model="from" class="me-5" type="text" id="from">
      <label class="me-3" for="to">Sihtkoht</label>
      <input v-model="to" type="text" id="to">

    </div>
    <div>
      <button @click="goToSearchView" class="btn btn-dark">Otsi reise</button>
    </div>
  </div>
</template>

<script>

import router from "@/router";

export default {
  name: 'HomeView',
  data() {
    return {
      from: '',
      to: ''
    }
  },
  methods: {
    goToSearchView() {
      router.push({name: 'searchRoute'})
    },

  }
}
</script>
