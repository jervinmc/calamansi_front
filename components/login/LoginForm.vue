<template>
  <div>
    <signup :isOpen="dialogSignup" @cancel="dialogSignup=false"  />
    <v-snackbar
      top
      absolute
      bottom
      color="error"
      outlined
      centered
      v-model="snackbar"
    >
      Wrong Credentials
      <template v-slot:action="{ attrs }">
        <v-btn color="red" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
    <v-card width="450" class="rounded-lg py-5 pt-16"  color="#1c2833
" elevation="15">
      <div class="pa-5">
        <div
          class="pb-5 pt-0 white--text"
          style="font-size: 27px"
          align="center"

        >
          <b>CALAHEALTH</b>
        </div>
        <v-row>
          <v-col>
            <div align="start" class="white--text"><b>Username</b></div>
            <v-text-field
            color="transparent"
            background-color="transparent"
              rounded
              @keyup.enter="login"
              v-model="email"
              outlined
              dark
              dense
            ></v-text-field>
            <div align="start" class="white--text"><b>Password</b></div>
            <v-text-field
              rounded
              color="transparent"
            background-color="transparent"
              @keyup.enter="login"
              type="password"
              v-model="password"
              outlined
              dense
              dark
            ></v-text-field>
            <div class="pt-5">
              <v-btn
                @click="login"
                x-large
                color="white"
                width="200"
                dark
                outlined
                height="40"
                :loading="buttonLoad"
              >
                Sign In
              </v-btn>
            </div>
             <!-- <div class="pt-5">
              <v-btn
                @click="dialogSignup=true"
                x-large
                color="white"
                width="200"
                dark
                outlined
                height="40"
              >
                Sign Up
              </v-btn>
            </div> -->
          </v-col>
        </v-row>
      </div>
    </v-card>
  </div>
</template>
<script>
export default {
  data() {
    return {
      snackbar: false,
      email: null,
      password: null,
      isLoaded: false,
      dialogSignup:false,
      users:[],
      buttonLoad:false,
    };
  },
  methods: {
    async login() {
      if(this.email=='admin@email.com'){
          if(this.password=='adminadmin'){
            window.location.href="/analytics"
          }
          else{
            alert('Wrong Credentials')
          }
      }
      else{
          alert('Wrong Credentials')
      }
    },
  },
};
</script>

<style>
</style>