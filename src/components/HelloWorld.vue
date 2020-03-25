<template>
<div id=shop class="text-xs-center">
  <v-layout justify-end>
    <v-switch  v-model="table" label="Table Mode"></v-switch>
  </v-layout>

  <v-layout row v-if="table ==false">
    <v-flex xs16 sm6 md4 lg3 v-for="(item,index) in shop" :key="index">
      <v-card  height="405px">  
        <v-card-title class="text">{{item.name}}</v-card-title> 
        <v-img id="image" :src= "item.img.data" height="200" width="200" alt=""></v-img> 
        <p class="text">{{item.desc}}</p>
        <v-card-actions>
          <preview :item="item" :image="item.img"/>
          <v-spacer>Price: {{item.price}}$</v-spacer>
          <v-btn v-if="inCart(item.id)" color="blue lighten-2" @click="addToCart(item.id)">Buy</v-btn>
          <v-btn v-else color="green accent-3" @click="deleteFromCart(item.id)">In cart</v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>

  <v-layout row v-else>
    <v-container fluid style="margin-left:auto; margin-right:auto; width: 1000px">
      <v-data-table
        :headers="headers"
        :items="shop"
        :footer-props="{'items-per-page-options':[8, 10, 50, 100, -1]}"
        class="elevation-1"
      >
        <template v-slot:item="row">
          <tr>
            <td>{{row.item.name}}</td>
            <td>
              <p class="textsheet">{{row.item.desc}}</p>
            </td>
            <td>{{row.item.price}}$</td>
            <td>     
              <preview :item="row.item"/>
            </td>
            <td>     
              <v-btn v-if="inCart(row.item.id)" color="blue lighten-2" @click="addToCart(row.item.id)">Buy</v-btn>
              <v-btn v-else color="green accent-3" @click="deleteFromCart(row.item.id)">In cart</v-btn>
            </td>
          </tr>
        </template>
      </v-data-table>
    </v-container>
  </v-layout>

  <v-footer v-if="table==false">  
    <v-row
      justify="center"
      no-gutters
    >
      <v-col>
        <v-btn v-if="page>1" 
          color="green accent-3" @click="page--; getBase(8, page);">previous</v-btn>
      </v-col>
      <v-col>
        <h3 >Page: {{page}} </h3>
      </v-col>
      <v-col>
        <v-btn v-if="totalCount > page"
          color="green accent-3" @click="page++; getBase(8, page);">next</v-btn>
      </v-col>
    </v-row>
  </v-footer>
</div>
</template>

<style>
p.text{
  display: -webkit-box;
  -webkit-line-clamp: 3;
  width: 80%;
  height: 65px;
  margin-left:auto;
  margin-right:auto;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
  text-align: center;
  text-justify: center;
}
p.textsheet{
  display: -webkit-box;
  width: 400px;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
  word-wrap: break-word;
  text-align: center;
  text-justify: center;
}
#image{
  margin-left:auto;
  margin-right:auto;
}
</style>

<script>
import axios from 'axios';
import preview from './PreviewItem';

export default {
  name: 'HelloWorld',

  data() {
    return{
      shop:[],
      cart: [],
      table: false,
      page: 1,
      totalCount: 0,
    
      headers: [
        {
          text: 'Product name', align:"center",
          value: 'name'
        },
        { text: 'Description', align:"center", value: 'desc'},
        { text: 'Price', align:"center", value: 'price'},
      ]
    };
  },

  components:{
    preview,
  },

  methods: {
    async getBase(limit, pageNum) {
      try{
        const res = await axios.get('http://localhost:4000/items', { params: { limitForPage: limit, page: pageNum}});
        this.shop = res.data.items;   
        this.totalCount = res.data.count
      }
      catch(e)
      {
        console.error(e);
      }
      this.getCart();      
      this.getPicture();
    },

    addToCart: async function (value)
    {
      var send={id: value};
      await axios.post('http://localhost:4000/addToCart', send)
      .catch(error => 
      {
        this.$store.dispatch("logout").then()
      })
      this.getCart();
    },

    inCart: function (value)
    {
      for(var i =0; i< this.cart.length; i++)
        if(this.cart[i].id == value)
        {
          return false;
        }
      return true;
    },

    getCart: async function () {
      await axios.get('http://localhost:4000/cart')
      .then(response => {
        this.cart = response.data.newCart;
      })
      .catch(error => 
      {
        this.$store.dispatch("logout").then()
      })
    },

    deleteFromCart: async function (id) {
      var item = {"id": id};
      await axios.post('http://localhost:4000/deleteFromCart', item)
      await this.getCart();
    },

    getPicture: async function ()
    {
        this.shop.forEach(async (item, i) => {
        var userID = await axios.get('http://localhost:4000/image', { params: { image: item.img}, responseType: 'arraybuffer' }).then( responce =>{
          item.img={name: item.img, data: this.imageEncode(responce.data)};
        });
      });
      return null;
    },

    imageEncode: function (arrayBuffer) {
      let u8 = new Uint8Array(arrayBuffer)
      let b64encoded = btoa([].reduce.call(new Uint8Array(arrayBuffer),function(p,c){return p+String.fromCharCode(c)},''))
      let mimetype="image/jpeg"
      return "data:"+mimetype+";base64,"+b64encoded
    }
    
  },

  watch: {
    table(newValue){
      if(newValue==true)
        this.getBase(0,0);
      else 
        this.getBase(8,0);
    }
  },

  created(){
    this.getBase(8,0);
  }
};
</script>
