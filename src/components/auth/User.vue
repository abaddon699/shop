<template>
  <div class="text-center">
    <v-menu offset-y>
      <template v-slot:activator="{ on }">
        <v-btn
          dark
          v-on="on"
        >
          {{name}}
        </v-btn>
      </template>
      <v-list>
        <v-list-item>
          <cart/>
        </v-list-item>
        <v-list-item>
          <insertItem/>
        </v-list-item>
        <v-list-item>
          <v-btn color="red" text @click="logout">Log Out</v-btn>
        </v-list-item>
      </v-list>
    </v-menu>
  </div>
</template>

 <script>
import axios from 'axios'
import cart from '../Cart'
import insertItem from '../InsertItem'

export default {    
  data(){
    return{
      name: ''
    }
  },

  components: {
    cart,
    insertItem
  },

  methods: {
    logout: function() {
      this.$store.dispatch("logout").then();
    },

    getuser: async function() {
      var userID = await axios({ url: 'http://localhost:4000/username', method: 'GET' })
        .then(response => {
          this.name = response.data['id'];
        })
        .catch(error => this.$store.dispatch("logout").then())
      }
    },

  mounted()
  {
    this.getuser()
  }
}; 
</script>