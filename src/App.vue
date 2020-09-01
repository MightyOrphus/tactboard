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
  /* font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px; */
}

.noselect {
  -webkit-touch-callout: none; /* iOS Safari */
  -webkit-user-select: none; /* Safari */
  -khtml-user-select: none; /* Konqueror HTML */
  -moz-user-select: none; /* Old versions of Firefox */
  -ms-user-select: none; /* Internet Explorer/Edge */
  user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome, Edge, Opera and Firefox */
}
</style>
