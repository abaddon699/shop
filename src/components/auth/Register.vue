<template>
  <v-dialog v-model="RegPop" max-width="600px">
    <template v-slot:activator="{ on }">
      <v-btn color="red"
        text
        v-on="on">
          Sign In
        </v-btn>
    </template>
    <v-card>
      <v-card-title>
        <span class="headline">User Profile</span>
      </v-card-title>
      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12" sm="6" md="4">
              <v-text-field label="Nickname*" v-model="name"></v-text-field>
            </v-col>
            <v-col cols="12">
              <v-text-field label="Password" 
                type="password" 
                v-model="password"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <v-text-field label="password-confirm" 
                type="password" 
                v-model="password_confirmation" 
              ></v-text-field>
            </v-col>
          </v-row>
        </v-container>
        <small>*indicates required field</small>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" text @click="RegPop = false">Close</v-btn>
        <v-btn color="blue darken-1" text @click="register">Save</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>>

<script>
export default {
  data() {
    return {
      RegPop: false,
      name: "",
      password: "",
      password_confirmation: "",
    };
  },

  methods: {
    register: function() {
      if(this.password != this.password_confirmation)
        return alert("Password is different")

      let data = {
        name: this.name,
        password: this.password,
      };

      this.$store
        .dispatch("register", data)
        .catch(err => console.log(err));
    }
  }
};
</script>