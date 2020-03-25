<template>
  <v-toolbar  dense dark>
    <v-btn to="/">Home</v-btn>
    <v-btn to="/about">About</v-btn>

    <v-spacer></v-spacer>
 
    <span v-if="isLoggedIn"> <user/></span>
    <span v-else> <login/></span>
      
  </v-toolbar>
</template>>

<script>
import user from './auth/User';
import login from './auth/Login';

export default {
  components:{
    user,
    login
  },
  
  computed: {
    isLoggedIn: function() {
    return this.$store.getters.isLoggedIn;
  }}
};
</script>