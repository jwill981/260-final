<template>
  <div class="add">
    <h1>Add Items To Your Registry</h1>
    
    <div class="items">
      <div class="flex-container">
        <div class="flex-child">
        <h2>Couple Information</h2>
        <h4>Couple Name: {{couple.name}}</h4>
        <h4>Event Date: {{couple.date}}</h4>
        <h4>Address: {{couple.address}}</h4>
        <button @click="edit()">Edit</button>
      </div>

        <div class="flex-child">
          <div class="editCouple" v-if="editButton">
            <form @submit.prevent="editCouple">
              <label>Couple Name: </label><input type="text" v-model="coupleName"><br>
              <label>Event Date: </label><input type="text" v-model="date"><br>
              <label>Address: </label><input type="text" v-model="address"><br>
              <button type="submit">Apply Changes</button>
            </form>
          </div>
        </div>
      </div>

      <h2>Add an Item To Your Registry</h2>
      <div class="flex-container">
        <div class="flex-child">
          <form @submit.prevent="addItem">
            <label>Item Name: </label><input type="text" v-model="itemName"><br>
            <label>Item Description: </label><input type="text" v-model="itemDescription"><br>
            <label>Upload Picture: </label><input type="file" name="photo" @change="fileChanged"><br>
            <button type="submit">Add Item</button>
          </form>
        </div>

        <div class="flex-child">
          <div class="upload" v-if="addedItem">
            <h3>{{addedItem.name}}</h3>
            <p>{{addedItem.description}}</p>
            <img :src="addedItem.path" width="200px"/>
            <p></p>
            <button @click="addAnotherItem">Add Another Item</button>
          </div>
        </div>
      </div>
    </div>

    <h3>Edit an Item on Your Registry</h3>
    <div class="edit">
          <div class="item-form">
            <input v-model="findName" placeholder="Search" />
              <div class="suggestions" v-if="suggestions.length > 0">
                <div
                  class="suggestion"
                  v-for="s in suggestions"
                  :key="s.id"
                  @click="selectItem(s)"
                >
                  {{ s.name }}
                </div>
              </div>
          </div>

          <div class="upload" v-if="findItem">
            <label>Item Name: </label><input v-model="newItemName" />
            <p></p>
            <label>Item Description: </label><textarea v-model="newItemDescription" />
            <p></p>
            <img :src="findItem.path" />
            <p></p>
            <button @click="deleteItem(findItem)">Delete</button>
            <button @click="editItem(findItem)">Edit</button>
          </div>

      </div>
    </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Add",
  data() {
    return {
      addButton: null,
      editButton: null,
      couple: null,
      coupleName: "",
      date: "",
      address: "",
      items: [],
      itemName: "",
      itemDescription: "",
      file: null,
      addedItem: null,
      findName: "",
      findItem: null,
      newItemName: "",
      newItemDescription: ""
    };
  },
  created() {
    this.getCouple();
  },
  computed: {
    suggestions() {
      let items = this.items.filter(item => item.name.toLowerCase().startsWith(this.findName.toLowerCase()));
      return items.sort((a, b) => a.name > b.name);
    }
  },
  methods: {
    async getCouple(){
      try {
        let response = await axios.get(`/api/couple/${this.$root.$data.user._id}`);
        this.couple = response.data;
        this.getItems();
      } catch (error) {
        //console.log(error);
      }
    },
    addAnotherItem() {
      this.addedItem = null;
      this.itemName = "";
      this.itemDescription = "",
      this.file = null;
    },
    edit() {
      this.editButton = true;
    },
    async editCouple() {
      try {
        let response = await axios.put(`/api/couples/${this.couple._id}`, {
          name: this.coupleName,
          date: this.date,
          address: this.address
        });
        this.couple = response.data;
        this.editButton = false;
      } catch (error) {
        //console.log(error);
      }
    },
    add() {
      this.couple = null;
      this.addButton = true;
    },
   async getItems() {
      try {
        const response = await axios.get(`api/items/${this.couple._id}`);
        this.items = response.data;
      } catch (error) {
       // console.log(error);
      }
    },
    fileChanged(event) {
      this.file = event.target.files[0];
    },
    async addItem() {
      try {
        const formData = new FormData();
        formData.append("photo", this.file, this.file.name);
        let r1 = await axios.post("/api/photos", formData);
        let r2 = await axios.post(`/api/items/${this.couple._id}`, {
          name: this.itemName,
          bought: false,
          path: r1.data.path,
          description: this.itemDescription,
        });
        this.addedItem = r2.data;
        this.getItems();
      } catch (error) {
       // console.log(error);
      }
    },
    selectItem(item) {
      this.findName = "";
      this.findItem = item;
    },
    async deleteItem(item) {
      try {
        await axios.delete(`/api/items/${item._id}`);
        this.findItem = null;
        this.getItems();
        return true;
      } catch (error) {
        //console.log(error);
      }
    },
    async editItem(item) {
      try {
          await axios.put(`/api/items/${item._id}`, {
            name: this.newItemName,
            description: this.newItemDescription,
          });
          this.findItem = null;
          this.newItemName = null;
          this.newItemDescription = null;
          this.getItems();
          return true;
      } catch (error) {
       // console.log(error);
      }
    }
  },
};
</script>

<style scoped>

button {
  margin-top: 15px;
  margin-right: 10px;
  padding: 5px;
}

.flex-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.flex-child:first-child {
    margin-right: 40px;
} 

.addButton {
  font-weight: bold;
  font-size: 18px;
  color:#0062ff;
  margin-bottom: 10px;
}

.editCouple {
  padding-top: 20px;
}

.upload img {
  max-width: 300px;
}

.item-form {
  margin-right: 40px;
}

.edit {
  display: flex;
  justify-content: center;
}

input,
textarea,
select,
button {
  font-family: "Montserrat", sans-serif;
  font-size: 1em;
  margin: 5px;
}

.couple-button:focus {
  background-color: #7eb0da;
}

@media only screen and (max-width: 658px) {
  .flex-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-content: center;
  }

  .edit {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
}

</style>
