<template>
    <v-dialog v-model="CartDialog" max-width="600px">
        <template v-slot:activator="{ on }">
        <v-btn color="blue"
      text
      v-on="on"
      @click.stop="getCart">
      Cart
      </v-btn>
      </template>
     <v-card>
        <v-card-title>
          <v-toolbar dark >
            <span class="headline">Your cart</span> 
          </v-toolbar>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row v-for="item in cartItems" :key="item.id">
                <v-col>
                <v-card>
                    <v-row justify="center" align="center"> 
                        <v-col>
                            <v-label >{{item.name}}</v-label>
                        </v-col>
                        <v-col>
                          <v-label>Price: {{item.price}}$</v-label>
                        </v-col>
                        <v-col>
                          <v-btn  color="red" text @click.stop="deleteFromCart(item.id)">Delete</v-btn>
                        </v-col>
                    </v-row>
                    <preview :item="item"/>
                </v-card>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions v-if="cartItems[0]!=undefined">
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="purchase">Purchase</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>>

<script>
import axios from 'axios'
import preview from './PreviewItem';

export default {
  data() {
    return {
      cartItems: [],
      CartDialog: false
    };
  },

  components:{
    preview,
  },

  methods: {
    purchase: async function() {
      var cart = await axios.post('http://localhost:4000/purchase')
      this.chartItems = cart.data.newCart;
      this.CartDialog = false;
    },

    getCart: async function () {
      var cart = await axios.get('http://localhost:4000/cart')
      cart=cart.data.newCart;
      this.cartItems = cart;
    },
        
    addToCart: async function () {
      var t = 5;
      var item = {"id": t};
      var userID = await axios.post('http://localhost:4000/addToCart', item)
      userID.data = "";
    },

    deleteFromCart: async function (id) {
      var item = {"id": id};
      await axios.post('http://localhost:4000/deleteFromCart', item)
      await this.getCart();
    }
  },

  created() {
    this.getCart();
  }
}; 
</script>