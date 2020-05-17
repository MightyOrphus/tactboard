<template>
  <div v-bind:id="id" class="dateCol" @dragovere.prevent @drop.prevent="drop">
    <div id="header">{{ date }}</div>
    <Card v-for="task in tasks" v-bind:key="task.issueId"></Card>
  </div>
</template>
<script>
import Card from "./Card.vue";
export default {
  props: { id: String, date: String, tasks: Array },
  components: {
    Card,
  },
  methods: {
    drop: (e) => {
      const card_id = e.dataTransfer.getData("card_id");
      const card = document.getElementById(card_id);
      card.style.display = "block";
      e.target.appendChild(card);
    },
  },
};
</script>

<style scoped>
#header {
  border-bottom: groove black;
  font-size: 20px;
  text-align: center;
  background-color: var(--backgrounds-2-hex);
}
</style>
