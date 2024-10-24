<template>

  <v-app>
    <v-app-bar app>
      <v-app-bar-nav-icon @click="toggleDrawer" class="mdi-account"></v-app-bar-nav-icon>

    </v-app-bar>

    <v-navigation-drawer v-if="isDrawerOpen" class="navigation-drawer" color="white" v-model="drawer" fixed app>

      <!-- <nav v-if="isDrawerOpen" class="navigation-drawer"> -->
      <img v-if="user" :src="user.photoURL" alt="Profile Picture" class="drawer-profile-pic" />
      <h1 v-if="user">{{ user.displayName }}</h1>
      <h6 v-if="user">{{ user.email }}</h6>
      <ul>
        <li><router-link to="/">Dashboard</router-link></li>
        <li><router-link to="/settings">Settings</router-link></li>
        <li><router-link to="/aboutus">About Us</router-link></li>
      </ul>


    </v-navigation-drawer>
  </v-app>
</template>

<script>
import { auth } from '/plugins/firebase.js';
import { onAuthStateChanged } from "firebase/auth";

export default {
  name: "profile",
  data() {
    return {
      user: null,
      isDrawerOpen: true,
    };
  },
  created() {
    onAuthStateChanged(auth, async (currentUser) => {
      if (currentUser) {
        console.log(currentUser);
        this.user = currentUser;
      } else {
        this.$router.push('/auth/login');
      }
    });
  },
  methods: {
    toggleDrawer() {
      this.isDrawerOpen = !this.isDrawerOpen;
    },
    logout() {
      auth.signOut().then(() => {
        this.$router.push('/');
      }).catch((error) => {
        console.error("Logout Error:", error);
      });
    },
  },
};
</script>

<style scoped>
.profile-header {
  cursor: pointer;
  display: flex;
  align-items: center;
}

.profile-header img {
  border-radius: 50%;
  width: 50px;
  height: 50px;
  margin-right: 10px;
}

.navigation-drawer {
  width: 250px;
  background-color: #f4f4f4;
  padding: 20px;
  position: fixed;
  height: 100%;
  left: 0;
  top: 0;
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
}

.drawer-profile-pic {
  border-radius: 50%;
  width: 80px;
  height: 80px;
  margin-bottom: 10px;
}

.navigation-drawer h1,
.navigation-drawer p {
  margin: 10px 0;
}

.navigation-drawer ul {
  list-style-type: none;
  padding: 0;
}

.navigation-drawer li {
  margin: 15px 0;
}

.navigation-drawer a {
  text-decoration: none;
  color: #333;
}

.navigation-drawer li:hover {
  background-color: #e0e0e0;
}
</style>
