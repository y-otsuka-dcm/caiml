<template>
  <div id="login-form">
    <router-view/>
    <input v-model="email" placeholder="edit me">
    <button v-on:click="createUser" class="btn btn-primary">POST</button>
    <!-- <script src="bundle.js"></script> -->
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
#loginform {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>