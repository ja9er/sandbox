<template>
  <div id="app">
<!--     <img alt="Vue logo" src="./assets/logo.png"> -->
    <mainpage msg="Welcome to Your Vue.js App"/>
<!--     <indexpage msg="Welcome to Your Vue.js App"/> -->
  </div>
</template>

<script>
//import Taskdata from './components/Taskdata.vue'
// import indexpage from './components/indexpage.vue'
import mainpage from './components/mainpage.vue';
// import test from './components/test.vue'

export default {
  name: 'App',
  components: {
    // indexpage,
     mainpage
  }
}
</script>

<style>

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
/*  margin-top: 60px; 控制加载的vue离顶部距离*/
}
</style>
