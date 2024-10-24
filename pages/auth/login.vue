<template>
  <div class="d-flex align-center justify-center" style="height: 100vh">
    <v-card width="450" class="mx-auto card-border" outlined>
      <v-img src="../logo.png" max-height="150" contain></v-img>

      <v-form ref="form" v-model="valid" lazy-validation @submit.prevent="submit()" class="mx-15">
        <v-text-field v-model="email" label="E-mail or Username" prepend-inner-icon="mdi-email" outlined
          required></v-text-field>
        <v-text-field v-model="password" :rules="passwordRules" label="Password" required
          :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'" :type="showPassword ? 'text' : 'password'"
          @click:append="showPassword = !showPassword" outlined prepend-inner-icon="mdi-lock"></v-text-field>
        <v-btn :disabled="!valid" color="primary" block type="submit">
          Sign In
        </v-btn>
      </v-form>

      <div class="hr-sect mx-15 my-5">or sign in with</div>

      <v-row class="mx-12 mt-5">
        <v-col cols="auto" class="mr-auto">
          <v-btn outlined color="blue-grey" @click="loginWithGoogle">
            <v-icon color="red" left>mdi-google</v-icon> Google
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn outlined color="blue-grey" @click="loginWithFacebook">
            <v-icon color="blue" left>mdi-facebook</v-icon> Facebook
          </v-btn>
        </v-col>
      </v-row>

      <v-card-actions class="mb-10 text-center">
        <v-spacer></v-spacer>
        <NuxtLink to="/auth/register" class="nuxt-link">Create an account</NuxtLink>
        <v-spacer></v-spacer>
      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
import { auth } from '/plugins/firebase.js';
import { signInWithEmailAndPassword, GoogleAuthProvider, signInWithPopup, FacebookAuthProvider } from 'firebase/auth';

export default {
  
  name: "login",
  layout: "auth",
  middleware: 'auth',
  auth: 'guest',
  data() {
    return {
      valid: true,
      password: "",
      email: "",
      emailRules: [
        (v) => !!v || "E-mail is required",
        (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
      ],
      passwordRules: [(v) => !!v || "Password is required"],
      showPassword: false,
    };
  },


  methods: {
    submit() {
      if (this.$refs.form.validate()) {
        this.login(this.email, this.password);
      }
    },

    async login(email, password) {
      try {
        const userCredential = await signInWithEmailAndPassword(auth, email, password);
        const user = userCredential.user;
        console.log('User Profile', userCredential.user);
        this.$store.commit('setUser', userCredential.user);
        this.$router.push('/');
        // Redirect or handle user session here
      } catch (error) {
        console.error(error);
        alert("Incorrect Credentials, Please Try Again");
      }
    },

    // login(email, password) {
    //   this.$auth.loginWith('local', {
    //     data: {
    //       identifier: email,
    //       password: password
    //     }
    //   })
    //   .then(res => {
    //     console.log('User Profile: ', res.data.user)
    //     console.log('User token: ', res.data.jwt)
    //   })
    //   .catch(error => {
    //     console.log(error)
    //   })
    // },

    async loginWithGoogle() {
      const provider = new GoogleAuthProvider();
      try {
        const result = await signInWithPopup(auth, provider);
        const user = result.user;
        console.log('User Profile', result.user);
        this.$store.commit('setUser', result.user);
        this.$router.push('/');
        // Redirect or handle user session here
      } catch (error) {
        console.error(error);
        alert("Failed to login with Google");
      }
    },

    async loginWithFacebook() {
      const provider = new FacebookAuthProvider();
      try {
        const result = await signInWithPopup(auth, provider);
        console.log('User Profile', result.user);
        this.$store.commit('setUser', result.user);
        this.$router.push('/');
        // Redirect or handle user session here
      } catch (error) {
        console.error(error);
        alert("Failed to login with Facebook");
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