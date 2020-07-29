<template>
  <div v-bind:id="id" class="card">
    <p id="taskSummary">{{ summary }}</p>
    <p id="taskDuration">{{ hoursInCurrentDate }}</p>
  </div>
</template>

<script>
export default {
  props: {
    id: String,
    summary: String,
    color: String,
    hoursInCurrentDate: Number,
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
