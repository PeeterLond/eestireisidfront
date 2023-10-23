<template>
  <div class="alert-master">
      <div v-if="alertMessage != null && alertMessage.length > 0" class="alert alert-success" role="alert" >
        {{alertMessage}}
      </div>
  </div>
</template>

<script>
export default {
  name: 'AlertSuccess',
  props: {
    alertMessage: String
  }
}
</script>
