<template>
  <div v-bind:id="id" class="dateCol noselect">
    <div id="header">{{ date }}</div>
    <template v-if="isWorkDay">
      <tr
        class="cellPair"
        v-for="index in numOfDev * expectedMaximumTaskPerDev +
          numOfTester * expectedMaximumTaskPerTester"
        :key="index"
      >
        <td
          id="leftCell"
          class="tableCell dropAllowed"
          @drop="onDrop"
          @dragover.prevent
          :style="{ 'z-index': zIndex }"
        ></td>
        <td
          id="rightCell"
          class="tableCell dropAllowed"
          @drop="onDrop"
          @dragover.prevent
          :style="{ 'z-index': zIndex }"
        ></td>
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
    this.$el.style.zIndex = this.zIndex;
  },
  props: {
    id: String,
    date: String,
    tasks: Array,
    numOfDev: Number,
    numOfTester: Number,
    isWorkDay: Boolean,
    emptyCell: Number,
    zIndex: Number,
  },
  data: () => {
    return {
      expectedMaximumTaskPerDev: 3,
      expectedMaximumTaskPerTester: 3,
    };
  },
  components: {},
  methods: {
    exportDate() {
      let taskList = new Array();
      let tabelCells = this.$el.querySelectorAll(".tableCell");
      tabelCells.forEach((cell) => {
        let children = cell.childNodes;
        if (children.length) {
          taskList.push(children[0].id);
        } else taskList.push({});
      });
      return {
        tasks: taskList,
        dateVal: this.date,
        isWorkDay: this.isWorkDay,
      };
    },
    addCard() {
      let tabelCells = this.$el.querySelectorAll(".tableCell");
      let count = 0;
      this.tasks.forEach((task) => {
        if (Object.keys(task).length > 0) {
          var ComponentClass = Vue.extend(Card);
          let newCard = new ComponentClass({
            propsData: {
              id: task.issueId,
              task: task,
            },
          });
          newCard.$mount();
          newCard.$el.draggable = "true";
          newCard.$el.addEventListener("dragstart", this.onDrag);
          newCard.$el.addEventListener("dragend", this.onDragEnd);
          newCard.$el.addEventListener("drop", this.appendAfter);
          tabelCells[count].appendChild(newCard.$el);
        }
        count++;
      });
    },
    onDrag(e) {
      requestAnimationFrame(function () {
        e.target.classList.add("hide");
      });
      e.dataTransfer.dropEffect = "move";
      e.dataTransfer.effectAllowed = "move";
      e.dataTransfer.setData("dragged_card", e.srcElement.id);
    },
    appendAfter(e) {
      console.log("appendAfter");
      console.log(e);
    },
    onDragEnd(e) {
      e.target.classList.remove("hide");
    },
    onDrop(e) {
      e.preventDefault();
      let targetClasses = e.target.className;
      if (
        targetClasses &&
        targetClasses.toLowerCase().includes("dropallowed")
      ) {
        let card_id = e.dataTransfer.getData("dragged_card");
        e.target.appendChild(document.getElementById(card_id));
        this.$emit("taskDropped");
      }
    },
  },
};
</script>

<style scoped>
#header {
  width: 170px;
  border-bottom: groove black;
  font-size: 20px;
  text-align: center;
  background-color: var(--backgrounds-2-hex);
}

.dateCol {
  display: table-cell;
  /* background-color: var(--backgrounds-3-hex); */
  border-right: 2px groove black;
  border-top: 2px groove black;
  position: relative;
  width: 170px;
  position: relative;
  overflow: visible;
}

.cellPair {
  height: 150px;
  width: 85px;
  position: relative;
  overflow: visible;
}

.tableCell {
  height: 150px;
  width: 85px;
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
