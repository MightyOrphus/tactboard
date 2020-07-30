<template>
  <div v-bind:id="id" class="card noselect">
    <p id="taskSummary">{{ summary }}</p>
    <p id="taskDuration">{{ oriEst }}</p>
  </div>
</template>

<script>
export default {
  props: {
    id: String,
    summary: String,
    color: String,
    oriEst: Number,
  },
  mounted() {
    this.setBackgroundColor();
  },
  methods: {
    setBackgroundColor() {
      this.$el.style.backgroundColor = this.color;
    },
  },
};
</script>

<style scoped>
.card {
  width: 150px;
  height: 148px;
  font-size: 15px;
  position: absolute;
}

.card:hover {
  z-index: 30000;
  border: 3px solid black;
}

#taskSummary {
  margin: 10 0 10 0;
  overflow: hidden;
  /* white-space: nowrap; */
  /* text-overflow: ellipsis; */
  width: 100%;
  height: 140px;
}

#taskDuration {
  margin: 0;
  border: 0;
}
</style>
