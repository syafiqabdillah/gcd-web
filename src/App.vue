<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/add-location">Add Location</router-link> | 
      <router-link to="/contact-us">Contact Us</router-link>
      <!-- <router-link to="/suggest-location">Suggest Location</router-link> |
      <router-link to="/dashboard">Dashboard</router-link> -->
    </div>
    <router-view/>
  </div>
</template>

<style>

html, body {
  background-color: #F3F3F3;
  
}

#app {
  font-family: roboto, Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #F3F3F3;
  
  
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #4F59B0;
}
</style>
