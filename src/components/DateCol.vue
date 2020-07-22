<template>
  <div v-bind:id="id" class="dateCol" @drop="onDrop($event)" @dragover.prevent @dragenter.prevent>
    <div id="header">{{ date }}</div>
    <Card
      v-for="(task, index) in tasks"
      :key="task.issueId + '_' + index"
      :id="task.issueId + '_' + index"
      :color="task.color"
      :summary="task.summary"
      :hoursInCurrentDate="task.hoursInCurrentDate"
      draggable="true"
      @dragstart.native="onDrag"
    ></Card>
  </div>
</template>
<script>
import Card from "./Card.vue";
export default {
  props: {
    id: String,
    date: String,
    tasks: Array
  },
  components: {
    Card
  },
  methods: {
    onDrag: e => {
      console.log("drag");
      e.dataTransfer.setData("dragged_card_id", e.srcElement.id);
    },
    onDrop: e => {
      console.log("drop");
      const card_id = e.dataTransfer.getData("dragged_card_id");
      const card = document.getElementById(card_id);
      console.log(card_id);
      console.log(card);
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
