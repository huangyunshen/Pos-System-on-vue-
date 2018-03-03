<template>
  <div id="app">
    <leftNav></leftNav>

    <router-view/>
  </div>
</template>

<script>
  import leftNav from 'com/leftNav'
  export default {
    name: 'App',
    components:{
      leftNav
    }
  }
</script>

<style>
  *{
    margin:0;
    padding: 0;
  }
  ul>li,ol>li{
    list-style: none;
  }
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }
</style>
