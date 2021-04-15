<template>
  <div class="add">
    <Add v-if="user" />
    <Login v-else />
  </div>
</template>

<script>
import Add from '@/components/Add.vue';
import Login from '@/components/Login.vue';
import axios from 'axios';

export default {
  name: 'dashboard',
  components: {
    Add,
    Login
  },
  async created() {
    try {
      let response = await axios.get('/api/users');
      this.$root.$data.user = response.data.user;
    } catch (error) {
      this.$root.$data.user = null;
    }
  },
  computed: {
    user() {
      return this.$root.$data.user;
    }
  }
}
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
