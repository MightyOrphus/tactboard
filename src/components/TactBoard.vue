<template>
  <div class="tactboard">
    <div id="myDateRange">
      <label for="startDate">Start Date:</label>
      <input type="date" id="startDate" format="yyyy-MM-dd" />
    </div>
    <div>
      <label for="sprintRange">Sprint range: </label>
      <input type="text" id="sprintRange" value="2" />
    </div>
    <button v-on:click="drawABoard">Draw A Board</button>
    <div id="board" class="flexbox">
      <DateCol
        v-for="date in allDatesInSprint"
        v-bind:key="date.dateNum"
        class="dateCol"
      ></DateCol>
    </div>
  </div>
</template>

<script>
import DateCol from "./DateCol.vue";
// import Card from "./Card.vue";
export default {
  mounted: function() {
    this.initStartDate();
  },
  components: {
    DateCol,
    // Card,
  },
  data: function() {
    return { allDatesInSprint: new Array() };
  },
  methods: {
    initStartDate() {
      var startDate = localStorage.getItem("startDate");
      var startDateEle = document.getElementById("startDate");
      if (startDate) startDateEle.value = startDate;
      else startDateEle.value = this.getCurrentDate();
    },
    getCurrentDate() {
      var m = new Date();
      return (
        m.getUTCFullYear() +
        "-" +
        ("0" + (m.getUTCMonth() + 1)).slice(-2) +
        "-" +
        ("0" + m.getUTCDate()).slice(-2)
      );
    },
    setAllDatesInSprint(startDate, endDate) {
      this.allDatesInSprint = new Array();
      var dateHolder = new Date(startDate.getTime());
      var dateNum = 1;
      while (dateHolder < endDate) {
        if (dateHolder.getDay != 6 && dateHolder.getDay() != 0) {
          this.allDatesInSprint.push({
            dateNum: dateNum++,
            date: new Date(dateHolder),
          });
        }
        dateHolder.setDate(dateHolder.getDate() + 1);
      }
      console.log(this.allDatesInSprint);
    },
    findEndDate(startDate, numberOfWeeks) {
      var endDate = new Date(startDate.getTime());
      return endDate.setDate(endDate.getDate() + numberOfWeeks * 7);
    },
    drawABoard() {
      var startDate = new Date(document.getElementById("startDate").value);
      var numberOfWeeks = document.getElementById("sprintRange").value;
      var endDate = this.findEndDate(startDate, numberOfWeeks);
      this.setAllDatesInSprint(startDate, endDate);
    },
  },
};
</script>

<style scoped>
.flexbox {
  display: flex;
  /* justify-content: space-between; */

  /* width: 100%; */
  height: 100vh;

  overflow: scroll;
  padding: 15px;
}

.flexbox .dateCol {
  background-color: var(--backgrounds-3-hex);
  border-style: groove;
  border-color: black;
  margin-left: 10px;
  min-width: 100px;
}

.flexbox .dateCol .card {
  background-color: var(--backgrounds-2-hex);
}
</style>
