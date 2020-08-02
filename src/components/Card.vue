<template>
  <div v-bind:id="id" class="card noselect" @mouseenter="mouseenter" @mouseleave="onMouseLeave">
    <p id="taskSummary">{{ task.summary }}</p>
    <p id="taskDuration">{{ task.oriEst }}</p>
  </div>
</template>

<script>
import bus from "../bus.js";
export default {
  props: {
    id: String,
    task: Object,
  },
  data: function () {
    return {
      timer: null,
    };
  },
  mounted() {
    this.setBackgroundColor();
  },
  methods: {
    setBackgroundColor() {
      this.$el.style.backgroundColor = this.task.color;
    },
    mouseenter() {
      this.timer = setTimeout(() => {
        let elementRect = this.$el.getBoundingClientRect();
        this.showTaskPopup(elementRect.left, elementRect.top);
      }, 1000);
    },
    showTaskPopup(x, y) {
      let taskPopup = document.getElementsByClassName("taskPopup");
      if (taskPopup && taskPopup.length) {
        let target = taskPopup[0];
        target.style.display = "block";
        target.style.left = x + "px";
        target.style.top = y - 100 + "px";
        bus.$emit("undateTaskPopup", this.task.summary, this.task.oriEst);
      }
    },
    onMouseLeave() {
      clearTimeout(this.timer);
      this.hideTaskPopup();
    },
    hideTaskPopup() {
      let taskPopup = document.getElementsByClassName("taskPopup");
      if (taskPopup && taskPopup.length) {
        taskPopup[0].style.display = "none";
      }
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
  border: 3px solid black;
}

#taskSummary {
  margin: 3px;
  overflow: hidden;
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
