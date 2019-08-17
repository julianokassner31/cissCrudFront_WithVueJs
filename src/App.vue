<template>
  <v-app>
    <v-content>
       <v-bottom-navigation 
        grow
        color="teal">
        <router-link to="/">
          <v-btn>
            <v-icon>Home</v-icon>
          </v-btn>
        </router-link>
  
        <router-link to="/About">
          <v-btn>
            <v-icon>About</v-icon>
          </v-btn>
        </router-link>
  
      </v-bottom-navigation>
      <router-view></router-view>
    </v-content>
  </v-app>
</template>

<script lang="ts">
import Vue from 'vue';
export default Vue.extend({
  name: 'App',
  components: {
  },
  data: () => ({
    //
  }),
});
</script>
