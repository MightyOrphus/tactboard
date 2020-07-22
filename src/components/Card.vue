<template>
  <div v-bind:id="id" class="card">
    <p>{{ summary }}</p>
    <p>{{ hoursInCurrentDate }}</p>
  </div>
</template>

<script>
export default {
  props: {
    id: String,
    summary: String,
    color: String,
    hoursInCurrentDate: Number
  },
  mounted() {
    this.setBackgroundColor();
  },
  methods: {
    setBackgroundColor() {
      this.$el.style.backgroundColor = this.color;
    }
  }
};
</script>

<style scoped>
.card {
  max-width: 200px;
  border-bottom: groove black;
  padding: 5px;
}
</style>
