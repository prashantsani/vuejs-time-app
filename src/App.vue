<template>
  <div id="app">
    <p class="text-center">
      <img alt="Vue logo" src="./assets/logo.png">
    </p>
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
<style lang="scss">
// USE MODULAR SCALE ONLY FOR TYPOGRAPHY
// https://www.modularscale.com/?1&em&1.333

/* Global Styles and Resets */
@import url('https://fonts.googleapis.com/css2?family=Inconsolata:wght@500&display=swap');
*{
  margin: 0;
  padding: 0;
}
html{
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
html,body {
    height: 100%;
    color: white;
    overflow: hidden;
}
html, body, form, select, input, button, label{
  font-family: 'Inconsolata', monospace;
}
body {
    background: #0f3854;
    background: radial-gradient(ellipse at center,  rgba(#0a2e38,0.8) 0%, rgba(#000,0.8) 70%),
                url(./assets/time-bg-opimised.jpeg) no-repeat;
    background-size: cover;
    font-size: 16px;
}
.text-center{
  text-align: center;
}
h1{
  font-size: 4.209rem;
}
#app{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-70%);
}
</style>
