<template>
  <div :id="id" class="card noselect">
    <p id="taskSummary">{{ task.summary }}</p>
    <p id="taskDuration">{{ task.oriEst }}</p>
  </div>
</template>

<script>
export default {
  props: {
    id: String,
    task: Object,
  },
  mounted() {
    this.setBackgroundColor();
  },
  methods: {
    setBackgroundColor() {
      this.$el.style.backgroundColor = this.task.color;
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

.hide {
  transform: translateX(-9999px);
}
</style>
