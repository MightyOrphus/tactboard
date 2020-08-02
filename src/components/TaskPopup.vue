<template>
  <div class="tooltip">
    <p>{{ summary }}</p>
    <p>{{ oriEst }}</p>
  </div>
</template>
<script>
export default {
  data: function () {
    return {
      summary: "summaryHere",
      oriEst: "oriEstHere",
    };
  },
  methods: {},
};
</script>
<style scoped>
.tooltip {
  width: 150px;
  z-index: 50000;
  color: white;
  background-color: black;
  opacity: 0.8;
  display: none;
}
</style>