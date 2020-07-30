<template>
  <div v-bind:id="id" class="dateCol">
    <div id="header">{{ date }} - {{isWorkDay}}</div>
    <template v-if="isWorkDay">
      <tr
        class="cellPair"
        v-for="index in ( numOfDev * expectedMaximumTaskPerDev + numOfTester * expectedMaximumTaskPerTester) "
        :key="index"
      >
        <td id="leftCell" class="tableCell dropAllowed" @drop="onDrop" @dragover.prevent></td>
        <td id="rightCell" class="tableCell dropAllowed" @drop="onDrop" @dragover.prevent></td>
      </tr>
    </template>
    <template v-else>
      <div class="holidayCol"></div>
    </template>
  </div>
</template>
<script>
import Card from "./Card.vue";
import Vue from "vue";
export default {
  mounted: function () {
    this.addCard();
  },
  props: {
    id: String,
    date: String,
    tasks: Array,
    numOfDev: Number,
    numOfTester: Number,
    isWorkDay: Boolean,
    emptyCell: Number,
  },
  data: () => {
    return {
      expectedMaximumTaskPerDev: 3,
      expectedMaximumTaskPerTester: 3,
    };
  },
  components: {},
  methods: {
    addCard() {
      let tabelCells = this.$el.querySelectorAll(".tableCell");

      let count = 0;
      if (this.emptyCell > 0) count = Math.ceil(this.emptyCell) + 1;

      this.tasks.forEach((task) => {
        var ComponentClass = Vue.extend(Card);
        let newCard = new ComponentClass({
          propsData: {
            key: task.issueId,
            id: task.issueId,
            summary: task.summary,
            oriEst: task.oriEst,
            color: task.color,
          },
        });
        newCard.$mount();
        newCard.$el.draggable = "true";
        newCard.$el.addEventListener("dragstart", this.onDrag);
        tabelCells[count].appendChild(newCard.$el);
        if (task.oriEst == 3) count++;
        else count += 2;
      });
    },
    onDrag(e) {
      e.dataTransfer.dropEffect = "move";
      e.dataTransfer.effectAllowed = "move";
      e.dataTransfer.setData("dragged_card_id", e.srcElement.id);
    },
    onDrop(e) {
      e.preventDefault();
      let targetClasses = e.target.className;
      if (
        targetClasses &&
        targetClasses.toLowerCase().includes("dropallowed")
      ) {
        let card_id = e.dataTransfer.getData("dragged_card_id");
        e.target.appendChild(document.getElementById(card_id));
      }
    },
  },
};
</script>

<style scoped>
#header {
  width: 150px;
  border-bottom: groove black;
  font-size: 20px;
  text-align: center;
  background-color: var(--backgrounds-2-hex);
}

.dateCol {
  display: table-cell;
  background-color: var(--backgrounds-3-hex);
  border-right: 2px groove black;
  border-top: 2px groove black;
  position: relative;
  width: 150px;
  position: relative;
  overflow: visible;
}

.cellPair {
  height: 150px;
  width: 75px;
  position: relative;
  overflow: visible;
}

.tableCell {
  height: 150px;
  width: 75px;
  padding: 0;
  margin: 0;
  border-bottom: 2px groove black;
}

#leftCell {
  border-right: 2px groove black;
  position: relative;
}

.holidayCol {
  height: 100%;
  background-color: orange;
}
</style>
