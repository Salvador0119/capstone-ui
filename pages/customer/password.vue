<template>
    <v-container>
      <v-row>
        <v-col cols="12" md="4">
          <v-card app>
            <v-card-title class="text-h5">Account</v-card-title>
            <v-list>
              <v-list-item>
                <v-list-item-title><v-btn :to="'/customer/customerprofile'" text>Account</v-btn></v-list-item-title>
              </v-list-item>
              <v-list-item>
                <v-list-item-title><v-btn :to="'/customer/password'" text>Password</v-btn></v-list-item-title>
              </v-list-item>
            </v-list>
            <v-list dense>
              <v-list-item
                v-for="(item, index) in items"
                :key="index"
                :to="item.link"
              >
                <v-list-item-title>{{ item.text }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-card>
        </v-col>
        <v-col cols="12" md="8">
          <v-card>
            <v-card-title class="text-h5">Account Settings</v-card-title>
            <v-card-text>
                
              <v-row> <v-col cols="12" md="6">
                  <v-text-field outlined v-model="password" :rules="passwordRules" label="New Password" type="password"
                  prepend-inner-icon="mdi-lock" required ></v-text-field>
                </v-col>
                <v-col cols="12" md="6">
                    <v-text-field outlined v-model="confirmpassword" :rules="confirmpasswordRules" label="Confirm New Password"
                    type="password" prepend-inner-icon="mdi-lock" required></v-text-field>
                </v-col></v-row>
            </v-card-text>
            <v-card-actions>
              <v-btn color="primary" @click="updateData()">Update</v-btn>
              <v-btn color="secondary">Cancel</v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>

    </v-container>
  </template>
  
  <script>
  export default {
    name:"password",
    data() {
      return {
            password: '',
            confirmpassword: '',
            passwordRules: [
                v => !!v || 'Password is required',
                v => (v && v.length >= 6) || 'Password must be at least 6 characters',
            ],
            confirmpasswordRules: [
                v => !!v || 'Confirmation Password is required',
                v => v === this.password || 'Password must match'
            ],

      };
    },
    methods: {
        getDetails() {
  
  console.log(`Information`);
  this.$axios
    .get(`http://localhost:1337/api/signin-infos/${this.$route.params.id}`)
    .then((response) => {
      console.log(response.data.data);
      this.password = response.data.data.attributes.password;
      this.confirmpassword = response.data.data.attributes.confirmpassword;
    })
    .catch((error) => {
      console.log(error);
    });  
},




        updateData() {
        let payload = {
          data: {
            password: this.password,
            confirmpassword: this.confirmpassword,
          },
        };
        this.$axios
          .put(`http://localhost:1337/api/signin-infos/${this.$route.params.id}`, payload)
          .then((response) => {
            console.log(response);
            this.$refs.form.reset();
              this.successSnackbar = true;
              this.textSuccess = "Succesfully Updated! ";
          })
          .catch((error) => {
            console.log(error);
  
            if (error.response.data.error.message) {
                this.errorSnackbar = true;
                this.textError = "Duplicate Patient Number";
              }
          });
          if (this.$refs.form.validate()) {
          console.log("Successfully Added!");
          let payload = {
            data: {
            password: this.password,
            confirmpassword: this.confirmpassword,
            },
          };
          this.$axios
            .put(`http://localhost:1337/api/signin-infos/`, payload)
            .then((response) => {
              console.log(response);
              this.$refs.form.reset();
              this.successSnackbar = true;
              this.textSuccess = "Succesfully Updated! ";
            })
            .catch((error) => {
              console.log(error.response.data.error.message);
  
              if (error.response.data.error.message) {
                this.errorSnackbar = true;
                this.textError = "Duplicate Email Number";
              }
            });   
          }
  
          
      },
    },

    mounted() {
      this.getDetails();
    },
  };
  </script>