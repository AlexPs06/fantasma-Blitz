<template>
  <v-app>

   
 

    <v-main  class="blue-grey darken-4">
      <HelloWorld/>
    </v-main>
  </v-app>
</template>

<script lang="ts">
import Vue from 'vue';
import HelloWorld from './components/HelloWorld.vue';

export default Vue.extend({
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    
  }),
});
</script>
