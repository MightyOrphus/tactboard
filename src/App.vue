<template>
  <div id="app">
    <TactBoard />
  </div>
</template>

<script>
import TactBoard from "./components/TactBoard.vue";

export default {
  name: "App",
  components: {
    TactBoard,
  },
};
</script>

<style>
:root {
  --backgrounds-1-hex: #048abf;
  --backgrounds-2-hex: #04bfbf;
  --backgrounds-3-hex: #04bf7b;
  --backgrounds-4-hex: #038c5a;
  --backgrounds-5-hex: #f2d7b6;
}

#app {
  background-color: var(--backgrounds-1-hex);
  /* font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px; */
}
</style>
