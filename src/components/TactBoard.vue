<template>
  <div class="tactboard">
    <div id="inputForm">
      <div id="myDateRange">
        <label for="startDate">Start Date:</label>
        <input type="date" id="startDate" format="yyyy-MM-dd" />
      </div>
      <div>
        <label for="sprintRange">Sprint range: </label>
        <input type="text" id="sprintRange" value="2" />
      </div>
      <div>
        <label for="numOfDev">Number of Dev: </label>
        <input type="text" id="numOfDev" value="3" />
      </div>
      <div>
        <label for="numOfTester">Number Of Tester: </label>
        <input type="text" id="numOfTester" value="1" />
      </div>
      <div>
        <label>JIRA csv: </label>
        <input type="file" id="input" multiple />
      </div>
      <button v-on:click="drawABoard">Draw A Board</button>
    </div>
    <div id="board" class="flexbox">
      <DateCol
        v-for="date in allDatesInSprint"
        v-bind:key="date.dateNum"
        v-bind:date="date.dateVal"
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
    return { allDatesInSprint: new Array(), jiraContent: "" };
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
            dateVal: this.formatDate(new Date(dateHolder)),
          });
        }
        dateHolder.setDate(dateHolder.getDate() + 1);
      }
    },
    findEndDate(startDate, numberOfWeeks) {
      var endDate = new Date(startDate.getTime());
      return endDate.setDate(endDate.getDate() + numberOfWeeks * 7);
    },
    formatDate(date) {
      var d = new Date(date),
        month = "" + (d.getMonth() + 1),
        day = "" + d.getDate(),
        year = d.getFullYear();

      if (month.length < 2) month = "0" + month;
      if (day.length < 2) day = "0" + day;

      return [day, month, year].join("-");
    },
    drawABoard() {
      var startDate = new Date(document.getElementById("startDate").value);
      var numberOfWeeks = document.getElementById("sprintRange").value;
      var endDate = this.findEndDate(startDate, numberOfWeeks);
      this.setAllDatesInSprint(startDate, endDate);
      console.log();
    },
    readFileAsString() {},
    CSVToArray(strData, strDelimiter) {
      // Check to see if the delimiter is defined. If not,
      // then default to comma.
      strDelimiter = strDelimiter || ",";

      // Create a regular expression to parse the CSV values.
      var objPattern = new RegExp(
        // Delimiters.
        "(\\" +
          strDelimiter +
          "|\\r?\\n|\\r|^)" +
          // Quoted fields.
          '(?:"([^"]*(?:""[^"]*)*)"|' +
          // Standard fields.
          '([^"\\' +
          strDelimiter +
          "\\r\\n]*))",
        "gi"
      );

      // Create an array to hold our data. Give the array
      // a default empty first row.
      var arrData = [[]];

      // Create an array to hold our individual pattern
      // matching groups.
      var arrMatches = null;

      // Keep looping over the regular expression matches
      // until we can no longer find a match.
      while ((arrMatches = objPattern.exec(strData))) {
        // Get the delimiter that was found.
        var strMatchedDelimiter = arrMatches[1];

        // Check to see if the given delimiter has a length
        // (is not the start of string) and if it matches
        // field delimiter. If id does not, then we know
        // that this delimiter is a row delimiter.
        if (
          strMatchedDelimiter.length &&
          strMatchedDelimiter !== strDelimiter
        ) {
          // Since we have reached a new row of data,
          // add an empty row to our data array.
          arrData.push([]);
        }

        var strMatchedValue;

        // Now that we have our delimiter out of the way,
        // let's check to see which kind of value we
        // captured (quoted or unquoted).
        if (arrMatches[2]) {
          // We found a quoted value. When we capture
          // this value, unescape any double quotes.
          strMatchedValue = arrMatches[2].replace(new RegExp('""', "g"), '"');
        } else {
          // We found a non-quoted value.
          strMatchedValue = arrMatches[3];
        }

        // Now that we have our value string, let's add
        // it to the data array.
        arrData[arrData.length - 1].push(strMatchedValue);
      }

      // Return the parsed data.
      return arrData;
    },
  },
};
</script>

<style scoped>
#inputForm {
  width: 350px;
  padding: 15px;
  background-color: var(--backgrounds-3-hex);
  border-style: groove;
  border-color: black;
}

input,
button {
  margin-top: 3px;
}

.flexbox {
  display: flex;
  /* justify-content: space-between; */

  /* width: 100%; */
  height: 100vh;

  overflow: auto;
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
