<template>
  <div id="app">
    <h1>Wedding Registries</h1>
    <div id="nav">
      <router-link to="/">View All Registries</router-link> |
      <router-link to="/add">Add/Edit a Couple or Item</router-link> |
      <a href="https://github.com/jwill981/final">GitHub</a>
      <a class="logout" v-if="user" @click="logout"> | <u>Logout</u><i class="fas fa-sign-out-alt"></i></a>
    </div>
    <router-view/>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'App',
  computed: {
    user() {
      return this.$root.$data.user;
    },
  },
  methods: {
    async logout() {
      try {
        await axios.delete("/api/users");
        this.$root.$data.user = null;
      } catch (error) {
        this.$root.$data.user = null;
      }
    },
  }
}
</script>


<style>
i {
  padding-left: 10px;
}

#app {
  font-family: "Montserrat", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #7eb0da;
}

#nav a.router-link-exact-active {
  color: #0062ff;
}
</style>
