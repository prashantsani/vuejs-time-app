<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <TimeApp />
  </div>
</template>

<script>
import TimeApp from './components/TimeApp.vue';

export default {
  name: 'App',
  components: {
    TimeApp,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
