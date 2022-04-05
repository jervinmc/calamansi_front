<template>
  <div>
    <v-img src="/background.jpeg" light>
    <v-sheet color="transparent" class="d-flex justify-center align-center" height="85vh">
    <div align="center">
      <login-form />
    </div>
    </v-sheet>
    </v-img>
  </div>
</template>

<script>
import LoginForm from '../components/login/LoginForm.vue'
export default {
  components: { LoginForm },

}
</script>

<style>

</style>