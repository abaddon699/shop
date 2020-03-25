<template>
  <v-dialog max-width="600px">
    <template v-slot:activator="{ on }">
      <v-btn color="blue"
        fab dark
        text
        v-on="on">
          Log In
        </v-btn>
    </template>
    <v-card>
      <v-card-title>
        <span class="headline">User Profile</span>
      </v-card-title>
      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12">
              <v-text-field 
                v-model="name" 
                label="Nickname"  
                :rules="[() => name.length > 0 || 'Required field']">
              </v-text-field>
            </v-col>
            <v-col cols="12">
              <v-text-field 
                v-model="password" 
                label="Password" 
                type="password"  
                :rules="[() => password.length > 0 || 'Required field']">
              </v-text-field>
            </v-col>
          </v-row>
        </v-container>
        <small>*indicates required field</small>
      </v-card-text>
      <v-card-actions>
          <v-spacer></v-spacer>
          <register />
          <v-btn color="blue darken-1" text @click="login">Enter</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>>

<script>
import register from './Register';

export default {
  data() {
    return {
      name: "",
      password: "",
      showReg: false
    };
  },

  components:{
    register
  },

  methods: {
    login: function() {
      let name = this.name;
      let password = this.password;
      this.$store
        .dispatch("login", { name, password })
        .catch(err => console.log(err));
    }
  }
}; 
</script>