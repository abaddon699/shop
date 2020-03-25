<template>
  <v-dialog v-model="PreviewDialog" max-width="600px" @input="getPicture(item.img)">
    <template v-slot:activator="{ on }">
      <v-btn color="yellow lighten-2"
        v-on="on">
        Preview
      </v-btn>
    </template>
    <v-card>
      <v-card-title>{{item.name}}</v-card-title>
      <v-card-text>
        <v-container>
          <v-img :src= "item.img.data"></v-img> 
          <v-row>
            <v-col cols="12">
              <v-label> Description: </v-label>
            </v-col>
            <v-col cols="12">
              <v-label >{{item.desc}}</v-label>
            </v-col>
            <v-col cols="12">
              <v-label >Price: {{item.price}}</v-label>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue lighten-2"  @click="PreviewDialog=false">Ok</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>>

<script>
import axios from 'axios';

export default {
  props:{
      item: Object,
  },

  data(){
    return{
      PreviewDialog: false,
    }
  },

  methods:{
    getPicture: async function (value)
    {
      if(typeof(this.item.img)==typeof(""))
        await axios.get('http://localhost:4000/image', { params: { image: value}, responseType: 'arraybuffer' }).then(response => {
          this.item.img = {data: this.imageEncode(response.data)};
        })
      return null;
    },

    imageEncode: function (arrayBuffer) {
      let u8 = new Uint8Array(arrayBuffer)
      let b64encoded = btoa([].reduce.call(new Uint8Array(arrayBuffer),function(p,c){return p+String.fromCharCode(c)},''))
      let mimetype="image/jpeg"
      return "data:"+mimetype+";base64,"+b64encoded
    }
  }
}
</script>>