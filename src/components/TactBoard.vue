<template>
  <div class="tactboard">
    <div id="upperPart">
      <b-tabs id="inoutTab">
        <b-tab title="JiraInput">
          <div id="inputForm">
            <div id="myDateRange">
              <label for="startDate">Start Date:</label>
              <input type="date" id="startDate" format="yyyy-MM-dd" />
            </div>
            <div>
              <label for="sprintRange">Sprint range (unit: week(s)):</label>
              <input type="text" id="sprintRange" v-model="sprintRange" />
            </div>
            <div>
              <label for="numOfDev">Number of Dev(s):</label>
              <input type="text" id="numOfDev" v-model="numOfDev" />
            </div>
            <div>
              <label for="numOfTester"
                >Number Of Tester(s) (not supported yet):</label
              >
              <input type="text" id="numOfTester" v-model="numOfTester" />
            </div>
            <div>
              <label>JIRA csv:</label>
              <input type="file" id="csvFileInput" />
            </div>
          </div>
          <b-button v-on:click="processCSVFile" squared>Process CSV</b-button>
          <b-button
            v-on:click="clearBoard"
            id="clearBoardButton"
            squared
            variant="dark"
            >Clear</b-button
          >
        </b-tab>
        <b-tab title="SaveFile" active>
          <div>
            <label>JSON file:</label>
            <input type="file" id="jsonFileInput" />
          </div>
          <b-button v-on:click="importJSON" squared>Import</b-button>
          <b-button
            v-on:click="saveAsJSON"
            id="saveAsButton"
            squared
            variant="primary"
            >Export</b-button
          >
          <b-button
            v-on:click="clearBoard"
            id="clearBoardButton"
            squared
            variant="dark"
            >Clear</b-button
          >
        </b-tab>
      </b-tabs>
      <template v-if="storyInfo.length">
        <StoryList id="storyList" :stories="this.storyInfo" />
      </template>
    </div>
    <div id="board" class="flexbox">
      <DateCol
        v-for="(date, index) in allDatesInSprint"
        :key="index"
        :zIndex="10000 - index * 100"
        :date="date.dateVal"
        :tasks="date.tasks"
        :isWorkDay="date.isWorkDay"
        :numOfDev="numOfDev"
        :numOfTester="numOfTester"
        :emptyCell="date.emptyCell"
        class="dateCol"
      ></DateCol>
    </div>
  </div>
</template>

<script>
import DateCol from "./DateCol.vue";
import StoryList from "./StoryList.vue";
export default {
  mounted: function() {
    this.initStartDate();
  },
  components: {
    DateCol,
    StoryList,
  },
  data: function() {
    return {
      allDatesInSprint: new Array(),
      hoursInOneDayPerPerson: 6,
      tasksGroupedByDate: {},
      hourInSecond: 3600,
      storyInfo: new Array(),
      numOfDev: 3,
      numOfTester: 1,
      sprintRange: 2,
      defaultHoursForUnestimatedTask: 6,
    };
  },
  watch: {
    tasksGroupedByDate: function() {
      this.drawABoard(this.tasksGroupedByDate);
    },
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
    clearBoard() {
      this.allDatesInSprint = null;
      this.storyInfo = new Array();
      document.getElementById("storyList").stories = this.storyInfo;
    },
    importJSON() {
      var inputFile = document.getElementById("jsonFileInput").files[0];

      if (!inputFile) {
        alert("No input file selected.");
        return;
      }

      var reader = new FileReader();
      var that = this;
      reader.onload = function(event) {
        that.allDatesInSprint = JSON.parse(event.target.result);
      };
      reader.readAsText(inputFile);
    },
    saveAsJSON() {
      if (this.allDatesInSprint && this.allDatesInSprint.length > 0) {
        var a = document.createElement("a");
        var file = new Blob([JSON.stringify(this.allDatesInSprint)], {
          type: "text/plain",
        });
        a.href = URL.createObjectURL(file);
        a.download = "tactboard.json";
        a.click();
      } else {
        alert("No data to export!!!");
      }
    },
    setAllDatesInSprint(startDate, endDate, tasksGroupedByDate) {
      this.allDatesInSprint = new Array();
      var dateHolder = new Date(startDate.getTime());
      var dateNum = 0;
      while (dateHolder < endDate) {
        var isWorkDay = dateHolder.getDay() != 6 && dateHolder.getDay() != 0;
        if (isWorkDay) {
          let tasksInDate;
          if (dateNum < tasksGroupedByDate.length) {
            tasksInDate = tasksGroupedByDate[dateNum].tasks;
          } else {
            tasksInDate = [];
          }
          this.allDatesInSprint.push({
            tasks: tasksInDate,
            dateVal: this.formatDate(new Date(dateHolder)),
            isWorkDay: isWorkDay,
          });
          dateNum++;
        } else {
          this.allDatesInSprint.push({
            tasks: [],
            dateVal: this.formatDate(new Date(dateHolder)),
            isWorkDay: isWorkDay,
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
    drawABoard(tasksGroupedByDate) {
      var startDate = new Date(document.getElementById("startDate").value);
      var numberOfWeeks = document.getElementById("sprintRange").value;
      var endDate = new Date(this.findEndDate(startDate, numberOfWeeks));
      this.setAllDatesInSprint(startDate, endDate, tasksGroupedByDate);
    },
    processCSVFile() {
      var inputFile = document.getElementById("csvFileInput").files[0];

      if (!inputFile) {
        alert("No input file selected.");
        return;
      }

      var reader = new FileReader();
      var that = this;
      reader.onload = function(event) {
        var fileContent = that.CSVToArray(event.target.result);
        var taskList = that.convertCVToTaskList([...fileContent]);
        that.removeSprintLevel(taskList, fileContent);
        that.tasksGroupedByDate = that.distributeTasksToDate(taskList);
      };
      reader.readAsText(inputFile);
    },
    removeSprintLevel(taskList, fileContent) {
      var headers = fileContent.shift();
      var issueIdIdx = headers.indexOf("Issue id");
      var sumIdx = headers.indexOf("Summary");
      var sprintLvlId;
      for (var lineNum = 0; lineNum < fileContent.length; lineNum++) {
        var line = fileContent[lineNum];
        var summary = line[sumIdx];

        if (summary.toLowerCase().indexOf("sprint level") != -1) {
          sprintLvlId = line[issueIdIdx];
          break;
        }
      }
      if (sprintLvlId) {
        taskList.filter((task) => task.parentId != sprintLvlId);
      }
    },
    randomColor() {
      // cr. https://stackoverflow.com/questions/43193341/how-to-generate-random-pastel-or-brighter-color-in-javascript
      return (
        "hsl(" +
        360 * Math.random() +
        "," +
        (25 + 70 * Math.random()) +
        "%," +
        (75 + 20 * Math.random()) +
        "%)"
      );
    },
    convertCVToTaskList(fileContent) {
      var result = new Array();
      var headers = fileContent.shift();
      var parentIssueIdx = headers.indexOf("Parent id");
      var sumIdx = headers.indexOf("Summary");
      var issueIdIdx = headers.indexOf("Issue id");
      var issueKeyIdx = headers.indexOf("Issue key");
      var orgEstIdx = headers.indexOf("Original Estimate");
      var issueTypeIdx = headers.indexOf("Issue Type");
      var accountIdx = headers.indexOf("Custom field (Account)");

      var someRandomColor = this.randomColor();
      for (var lineNum = 0; lineNum < fileContent.length; lineNum++) {
        var line = fileContent[lineNum];

        if (!line[parentIssueIdx]) {
          // story line
          // random new color when parent issue change...
          someRandomColor = this.randomColor();
          if (line[sumIdx]) {
            this.storyInfo.push({
              summary: line[sumIdx],
              issueId: line[issueIdIdx],
              issueKey: line[issueKeyIdx],
              color: someRandomColor,
            });
          }
        } else {
          // task line
          if (line[accountIdx].includes("FSG20")) {
            // currently get only development task
            // QA task scenario is wait for implemented.
            var parentId = line[parentIssueIdx];
            var taskObj = {
              summary: line[sumIdx],
              issueId: line[issueIdIdx],
              issueKey: line[issueKeyIdx],
              oriEst: this.toHour(line[orgEstIdx]),
              type: line[issueTypeIdx],
              color: someRandomColor,
              parentId: parentId,
            };
            result.push(taskObj);
          }
        }
      }
      return result;
    },
    toHour(second) {
      if (second && second.length)
        return Math.floor(second / this.hourInSecond);
      else return this.defaultHoursForUnestimatedTask;
    },
    distributeTasksToDate(taskList) {
      let tasksGroupedByDate = new Array();
      if (taskList && taskList.length) {
        let possibleHoursInOneDay = this.hoursInOneDayPerPerson * this.numOfDev;

        let currentDate = {
          remainingHours: possibleHoursInOneDay,
          tasks: new Array(),
        };

        let hourDebts = new Array();
        let maxDebtPerDay = possibleHoursInOneDay;
        let task;

        while (taskList.length) {
          task = taskList.shift();

          if (this.hoursInOneDayPerPerson >= task.oriEst) {
            if (task.oriEst > currentDate.remainingHours) {
              this.addHourDebt(
                hourDebts,
                maxDebtPerDay,
                task.oriEst - currentDate.remainingHours
              );
            }
            currentDate.remainingHours -= task.oriEst;
            currentDate.tasks.push(task);
          } else {
            if (currentDate.remainingHours >= this.hoursInOneDayPerPerson) {
              this.addHourDebt(
                hourDebts,
                maxDebtPerDay,
                task.oriEst - this.hoursInOneDayPerPerson
              );
            } else {
              this.addHourDebt(
                hourDebts,
                maxDebtPerDay,
                task.oriEst - currentDate.remainingHours
              );
            }
            currentDate.remainingHours -= this.hoursInOneDayPerPerson;
            currentDate.tasks.push(task);
          }

          if (currentDate.remainingHours <= 0) {
            let copiedDate = currentDate;
            tasksGroupedByDate.push(copiedDate);
            let hourDebtForCurrentDate = hourDebts.shift();
            currentDate = {
              remainingHours: possibleHoursInOneDay - hourDebtForCurrentDate,
              tasks: new Array(),
            };
            // ceil() if there is an numerator after a dividing process (ex. debt 1 hr from yesterday = ceil to 1 cell)
            let emptyCell = Math.ceil(
              hourDebtForCurrentDate / (this.hoursInOneDayPerPerson / 2)
            );
            if (emptyCell > 0) {
              for (let i = 0; i < emptyCell; i++) currentDate.tasks.push({});
            }
          }
        }
      }
      return tasksGroupedByDate;
    },
    addHourDebt(hourDebt, maxDebtPerDay, hourToAdd) {
      if (hourDebt.length && hourDebt[hourDebt - 1] < maxDebtPerDay) {
        let remainingHourInThatDay = maxDebtPerDay - hourDebt[hourDebt - 1];
        hourDebt[hourDebt - 1] += remainingHourInThatDay;
        hourToAdd -= remainingHourInThatDay;
      }
      while (hourToAdd > 0) {
        if (hourToAdd >= maxDebtPerDay) {
          hourToAdd -= maxDebtPerDay;
          hourDebt.push(maxDebtPerDay);
        } else {
          hourDebt.push(hourToAdd);
          hourToAdd = 0;
        }
      }
    },
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
#upperPart {
  padding-left: 5px;
}

#inoutTab {
  float: left;
  width: 500px;
  background-color: var(--backgrounds-3-hex);
  border-style: groove;
  border-color: black;
}

#inputForm {
  padding: 5px;
}

input,
button {
  margin-top: 3px;
}

#saveAsButton,
#clearBoardButton {
  margin-left: 10px;
}

.flexbox {
  display: table;
  height: 100vh;

  overflow: auto;
}
</style>
