<template>
<div class="text-center">
  <v-dialog v-model="InsertDialog" max-width="700px">
    <template v-slot:activator="{ on }">
      <v-btn color="blue"
        text
        v-on="on">
        Insert item
      </v-btn>
    </template>
    <v-card width="700">
      <v-card-text>
        <v-container >
          <v-row>
            <v-text-field v-model="itemName" label="Name" type="text" placeholder="Product name."></v-text-field>
          </v-row>
          <v-row>
            <v-card width="400" height="300">
              <v-card-title>Image</v-card-title>
              <v-img :src="imageUrl" height="200" required></v-img>
            </v-card>
          </v-row>
          <v-row>
            <input type="file" style="display: none" ref="fileInput" accept="image/*" @change="onFilePicked">
            <br> <br> <br>
            <v-btn @click="onPickFile">Upload image</v-btn>
          </v-row>
          <v-row>
            <v-textarea v-model="descript" label="Description" type="text" placeholder="Write about your product."></v-textarea>
          </v-row>
          <v-row>
            <v-text-field v-model="priceItem" label="Price" type="text" required></v-text-field>
          </v-row>
          <v-row>
            <v-btn width="200" height="150" color="blue darken-1" @click.stop="itemPush">Add to shop</v-btn>    
          </v-row>
        </v-container>
      </v-card-text>    
    </v-card>
  </v-dialog>
</div>
</template>

<script>
import axios from 'axios';

export default {  
  data() {
    return {
      itemName: "",
      descript: "",
      image: null,
      imageUrl: "",
      priceItem: "",
      InsertDialog: false,
      input: true
    };
  },

  methods: {
    itemPush: async function() {
      this.input = false;
      if(!this.input){
        let err = "";
        let itemName;
        let descript;
        let image;
        let priceItem;
        var user = await this.getuser();
        this.itemName == "" ? err+= "Add the product name. " : itemName = this.itemName;
        this.descript == "" ? err+= "Add the product description. " : descript = this.descript;
        this.image == null ? err+= "Add the product image. " : image = this.image;
        this.priceItem == "" ? err+= "Add the product price. " : priceItem = this.priceItem;
        if(err != "") 
        {
          this.input = true;
          return alert(err); 
        }           
        else{           
          const formData = new FormData()
          formData.append("imagePost", image, itemName);
          axios.post('http://localhost:4000/fileUp', formData); 
          var send = {user: user, name: itemName, desc: descript, img:itemName+".jpg", price: priceItem};
          await axios.post('http://localhost:4000/regItem', send );
          this.itemName = "";
          this.descript = ""; 
          this.image = null;
          this.imageUrl = "";
          this.priceItem = "";
          this.InsertDialog = false;
        }  
      }
    },

    getuser: async function() {
      var userID = await axios({ url: 'http://localhost:4000/username', method: 'GET' })
      userID = userID.data['id'];
      return userID;
    },

    onPickFile (){
      this.$refs.fileInput.click();
    },

    onFilePicked (event) {
      const files = event.target.files
      let fileName= files[0].name;

      if(fileName.lastIndexOf('.') <= 0 )
        return alert('Please add a valid file!');

      const fileReader = new FileReader();
      fileReader.addEventListener('load', () => {
        this.imageUrl = fileReader.result;
      })
      fileReader.readAsDataURL(files[0]);
      this.image = files[0];
    }
  }
}
</script>