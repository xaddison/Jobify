<template>
<div>
  <v-sheet color="white" rounded="lg">
    <v-form v-model="formData.valid" @submit.prevent="onSubmit">
      <v-row justify="center" v-if="loadingState">
        <div class="text-center">
            <v-progress-circular
                indeterminate
                color="primary"
            ></v-progress-circular>
        </div>
      </v-row>
      <v-container>
        <div class="loginTitle text-h6 mb-3">Welcome Back</div>
        <v-row justify="center">
          <v-img
          src="../../assets/login.png"
          max-width="30%"
          aspect-ratio
          >
          </v-img>
        </v-row>
        <!-- email text field -->
        <v-row justify="center">
          <v-col cols="10">
            <v-text-field
              rounded-md
              outlined
              label="Email"
              dense
              type="email"
              v-model="formData.email"
              :rules="[required('email'), checkEmail()]"
            ></v-text-field>
          </v-col>
          <!-- passowrd text field -->
          <v-col cols="10">
            <v-text-field
              rounded-md
              outlined
              label="Password"
              dense
              type="password"
              v-model="formData.password"
              maxlength="30"
              counter="30"
              :rules="[required('password'), checkLength('password', 8)]"
            ></v-text-field>
          </v-col>
        </v-row>

        <!-- alert to show any errors returning from back server -->
        <v-row justify="center">
          <v-col cols = "10">
            <v-alert 
            id="backerr-alert" 
            v-if="errorMessage"
            dense
            outlined
            type="error">
                {{ errorMessage }}
            </v-alert>
          </v-col>
        </v-row>
        <!-- form submission button -->
        <v-row justify="center">
          <v-col cols="10">
            <v-btn
              block
              color="blue darken-4"
              type="submit"
              large
              class="white--text"
              :disabled="!formData.valid"
              >login
            </v-btn>
          </v-col>
        </v-row>
        <!-- forgot your password -->
        <v-row justify="center" justify-md="center" class="mb-2">
          <router-link
            :to="{
              path: '/password-reset/reset',
              query: { redirect: this.$route.query.redirect },
            }"
            class="blue--text"
            >Forgot Your password ? Let us remind you
          </router-link>
        </v-row>
        <!-- to sign up page -->
        <v-row justify="center" justify-md="center" class="mb-2">
          <div class="text-caption">New here ? Why not join us now ?</div>
          <router-link
            :to="{
              path: '/signup',
              query: { redirect: this.$route.query.redirect },
            }"
            class="blue--text text-caption"
            >Sign Up
          </router-link>
        </v-row>
      </v-container>
    </v-form>
  </v-sheet>
  </div>
</template>

<script>
export default {
  data() {
    return {
      formData: {
        valid: false,
        email: "",
        password: "",
      },
      errorMessage: "",
      loadingState: false,
      required(propertyType) {
        return (v) =>
          (v && v.length > 0) || `Please enter Your ${propertyType}`;
      },
      checkEmail() {
        return (v) =>
          (v && /.+@.+\..+/.test(v)) || "Please enter a valid email address !";
      },
      checkLength(propertyType, minLength) {
        return (v) =>
          (v && v.length >= minLength) ||
          `${propertyType} must be longer than ${minLength} characters`;
      },
    };
  },
  methods: {
    async onSubmit() {
      this.errorMessage = ""
      try {
        this.loadingState = true;
        let userType = await this.$store.dispatch("loginUser", this.formData)
        if(userType == 'admin') {
          this.$router.push({path: '/admin'})
        }
        else {
          this.$router.push({path: '/home'});
        }
      } 
      catch (error) {
        this.loadingState = false;
        console.log("an error occured")
        if(error.status === "fail") {
          this.errorMessage = error.msg
        }
        else {
          this.errorMessage = "Please try again later !"
        }
      }
    },
  },
};
</script>

<style scoped>
.loginTitle {
  text-align: center;
  font-size: 21px;
  padding-bottom: 10px;
}
</style>
