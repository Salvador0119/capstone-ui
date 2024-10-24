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
              <v-row>
                <v-col cols="12" md="6">
                  <v-text-field
                    v-model="name"
                    label="Name"
                    :rules="nameRules"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12" md="6">
                  <v-text-field
                    v-model="email"
                    label="Email"
                    :rules="emailRules"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" md="6">
                  <v-text-field
                    v-model="number"
                    label="Phone number"
                    :rules="numberRules"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
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
    name:"customerprofile",
    data() {
      return {
        name: '',
            email: '',
            number:'',
            nameRules: [
                v => !!v || 'Name is required',
                v => (v && v.length <= 50) || 'Name is Required',
            ],
            emailRules: [
                v => !!v || 'Email is required',
                v => /.+@.+\..+/.test(v) || 'Email must be valid',
            ],
            numberRules: [(v) => !!v || " Number  is required"],

      };
    },
    methods: {
        getDetails() {
  
  console.log(`Information`);
  this.$axios
    .get(`http://localhost:1337/api/signin-infos/${this.$route.params.id}`)
    .then((response) => {
      console.log(response.data.data);
      // this.patient_no = response.data.data.attributes.patient_no,
      this.name = response.data.data.attributes.name;
      this.email = response.data.data.attributes.email;
      this.number = response.data.data.attributes.number;
    })
    .catch((error) => {
      console.log(error);
    });  
},




        updateData() {
        let payload = {
          data: {
            name: this.name,
            email: this.email,
            number: this.number,
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
            name: this.name,
            email: this.email,
            number: this.number,
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




  <!-- <template>
    <v-container>
      <v-row>
        <v-col cols="12">
          <h1 class="text-h4 text-center">Account Settings</h1>
        </v-col>
        <v-col cols="12" md="4">
          <v-card>

            <v-list>
              <v-list-item>
                <v-list-item-title><v-btn :to="'/customer/customerprofile'" text>Account</v-btn></v-list-item-title>
              </v-list-item>
              <v-list-item>
                <v-list-item-title><v-btn :to="''" text>Password</v-btn></v-list-item-title>
              </v-list-item>
            </v-list>
          </v-card>
        </v-col>
        <v-col cols="12" md="8">
          <v-card>
            <v-card-title>
              <h2 class="text-h6">Account Settings</h2>
            </v-card-title>
            <v-card-text>
              <v-form>
                <v-row>
                  <v-col cols="12" md="6">
                    <v-text-field label="First Name" v-model="firstName"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6">
                    <v-text-field label="Last Name" v-model="lastName"></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="6">
                    <v-text-field label="Email" v-model="email"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6">
                    <v-text-field label="Phone number" v-model="phone"></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="6">
                    <v-text-field label="Company" v-model="company"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6">
                    <v-text-field label="Designation" v-model="designation"></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12">
                    <v-textarea label="Bio" v-model="bio"></v-textarea>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12">
                    <v-btn color="primary" @click="updateProfile">Update</v-btn>
                    <v-btn color="secondary" @click="cancel">Cancel</v-btn>
                  </v-col>
                </v-row>
              </v-form>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </template>
  
  <script>
  export default {
    data () {
      return {
        firstName: 'Vũ Doãn',
        lastName: 'Dũng',
        email: 'vudungit92@gmail.com',
        phone: '+91 9876543215',
        company: 'OCC',
        designation: 'UI Developer',
        bio: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Labore vero enim error similique quia numquam ullam corporis officia odio repellendus aperiam consequatur laudantium porro voluptatibus, itaque laboriosam veritatis voluptatum distinctio!'
      }
    },
    methods: {
      updateProfile () {
        // Implement your update profile logic here
        console.log('Update Profile Called')
      },
      cancel () {
        // Implement your cancel logic here
        console.log('Cancel Called')
      }
    }
  }
  </script> -->