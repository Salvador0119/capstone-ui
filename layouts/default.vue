<template>
  <v-app>
    <div>
      <v-toolbar dark prominent src="https://cdn.vuetifyjs.com/images/backgrounds/vbanner.jpg">
      
        <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
        
        <v-toolbar-title>EASYFIX MOTORSHOP</v-toolbar-title>

        <v-spacer></v-spacer>
        

      </v-toolbar>
    </div>

    
  
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