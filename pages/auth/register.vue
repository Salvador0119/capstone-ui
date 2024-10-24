<template>
  <div class="d-flex align-center justify-center" style="height: 100vh">
    <v-card width="450" class="mx-auto card-border" outlined>
      <v-img src="../logo.png" max-height="150" contain></v-img>
      <v-card-text>
        <v-form ref="form" v-model="valid" lazy-validation class="mx-10 mb-10">
          <!-- <v-text-field v-model="name" :rules="nameRules" label="Name" prepend-inner-icon="mdi-account" outlined
            required></v-text-field> -->
          <v-text-field v-model="email" :rules="emailRules" label="E-mail" prepend-inner-icon="mdi-email" outlined
            required></v-text-field>
          <v-text-field v-model="password" :rules="passwordRules" label="Password" type="password"
            @click:append="showPassword = !showPassword" :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
            :type="showPassword ? 'text' : 'password'" prepend-inner-icon="mdi-lock" outlined required></v-text-field>
          <v-btn :disabled="!valid" color="primary" block @click="submit">
            Create Account
          </v-btn>

          <div class="mt-5">
            Already have an account?
            <NuxtLink to="/auth/login" class="nuxt-link">Sign In</NuxtLink>
          </div>
        </v-form>
      </v-card-text>
    </v-card>

    <v-snackbar top right color="green" v-model="snackbar">
      Successfully created an account!
      <template v-slot:action="{ attrs }">
        <v-btn small icon v-bind="attrs" @click="snackbar = false">
          <v-icon small>mdi-close</v-icon>
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import { auth } from '/plugins/firebase.js';
import { createUserWithEmailAndPassword } from 'firebase/auth';

export default {
  name: "register",
  layout: "auth",
  data() {
    return {
      valid: true,
      snackbar: false,
      // name: "",
      email: "",
      password: "",
      // nameRules: [(v) => !!v || "Name is required"],
      emailRules: [
        (v) => !!v || "E-mail is required",
        (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
      ],
      passwordRules: [(v) => !!v || "Password is required"],
      showPassword: false,
    };
  },
  methods: {
    async submit() {
      if (this.$refs.form.validate()) {
        try {
          const userCredential = await createUserWithEmailAndPassword(auth, this.email, this.password);
          console.log('User registered:', userCredential.user);
          this.snackbar = true; // Show snackbar
          this.$refs.form.reset(); // Reset the form
          setTimeout(() => {
            this.$router.push("/auth/login"); // Redirect to login after a short delay
          }, 1500); // Delay for snackbar to show
        } catch (error) {
          console.error("Error during registration:", error);
          alert("Error: " + error.message);
        }
      }
    },
  },
};
</script>



<style scoped>
.nuxt-link {
  text-decoration: none;
  padding-top: 5px;
}

.card-border {
  border-radius: 20px;
  box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px;
}

.hr-sect {
  display: flex;
  flex-basis: 100%;
  align-items: center;
  color: rgba(0, 0, 0, 0.9);
  margin: 8px 0;
}

.hr-sect::before,
.hr-sect::after {
  content: "";
  flex-grow: 1;
  background: rgba(0, 0, 0, 0.15);
  height: 1px;
  font-size: 0;
  line-height: 0;
  margin: 0 8px;
}
</style>