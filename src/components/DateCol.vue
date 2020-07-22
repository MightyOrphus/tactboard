<template>
  <div v-bind:id="id" class="dateCol" @dragovere.prevent @drop.prevent="drop">
    <div id="header">{{ date }}</div>
    <div v-if="isWorkDay">
      <Card
        v-for="task in tasks"
        v-bind:key="task.issueId"
        :color="task.color"
        :summary="task.summary"
        :hoursInCurrentDate="task.hoursInCurrentDate"
        draggable="true"
      ></Card>
    </div>
  </div>
</template>
<script>
import Card from "./Card.vue";
export default {
  props: {
    id: String,
    date: String,
    tasks: Array,
    isWorkDay: Boolean
  },
  components: {
    Card
  },
  methods: {
    drop: e => {
      const card_id = e.dataTransfer.getData("card_id");
      const card = document.getElementById(card_id);
      card.style.display = "block";
      e.target.appendChild(card);
    }
  }
};
</script>

<style scoped>
#header {
  border-bottom: groove black;
  font-size: 20px;
  text-align: center;
  background-color: var(--backgrounds-2-hex);
}

.dateCol {
  display: table-cell;
  background-color: var(--backgrounds-3-hex);
  border-style: groove;
  border-color: black;
  min-width: 150px;
}
</style>
