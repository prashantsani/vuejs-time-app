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
html,body {
    height: 100%;
    color: white;
}
body {
    background: #0f3854;
    background: radial-gradient(ellipse at center,  #0a2e38  0%, #000000 70%);
    background-size: 100%;
    font-size: 16px;
}
p {
    margin: 0;
    padding: 0;
}
fieldset{
  border: none;
  outline: none;
  margin: 30px 0
}
label{
  font-size: 18px;
  margin-right: 30px;
}
select{
  height: 30px;
  font-size: 18px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-70%);
}
</style>
