<template>
  <div
    v-bind:id="id"
    class="card"
    v-bind:draggable="draggable"
    @dragstart="dragStart"
    @dragover.stop
  >
    <p>{{ summary }}</p>
    <p>{{ hoursInCurrentDate }}</p>
  </div>
</template>

<script>
export default {
  props: {
    id: String,
    draggable: String,
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
    dragStart(e) {
      const target = e.target;
      e.dataTransfer.setData("card_id", target.id);

      setTimeout(() => {
        target.style.display = "none";
      }, 0);
    },
  },
};
</script>

<style scoped>
.card {
  max-width: 200px;
  border-bottom: groove black;
  padding: 5px;
}
</style>
