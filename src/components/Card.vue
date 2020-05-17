<template>
  <div
    v-bind:id="id"
    class="card"
    v-bind:draggable="draggable"
    @dragstart="dragStart"
    @dragover.stop
  >
    <p>{{ summary }}</p>
  </div>
</template>

<script>
export default {
  props: { id: String, draggable: String, summary: String, color: String },
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
  margin: 0;
  padding: 0;
  border-bottom: black;
}
</style>
