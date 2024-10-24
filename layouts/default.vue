<template>
  <v-app>
    <!-- <v-navigation-drawer color="white" v-model="drawer" :mini-variant="miniVariant" :clipped="clipped" fixed app>

    </v-navigation-drawer> -->

    
  
    <v-main>

      <Nuxt />

    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [




      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: "",
    };
  },
};
</script>
<style scoped>
.profile {
  background: "primary"
}
</style>