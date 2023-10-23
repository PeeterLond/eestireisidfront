<template>
  <div class="master">
    <header>
      <h1 @click="goToHome" >Eesti Reisid</h1>
      <div>
        <button class="btn btn-dark me-5">Logi sisse</button>
      </div>
    </header>
    <main>
      <router-view></router-view>
    </main>
  </div>

</template>
<script>
import router from "@/router";

export default {
  methods: {
    goToHome() {
      router.push({name: 'homeRoute'})
    },
  }
}

</script>