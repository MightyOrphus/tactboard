<template>
  <div id="inputForm">
    <div id="myDateRange">
      <label for="startDate">Start Date:</label>
      <input type="date" id="startDate" format="yyyy-MM-dd" />
    </div>
    <div>
      <label for="sprintRange">Sprint range:</label>
      <input type="text" id="sprintRange" value="2" />
    </div>
    <div>
      <label for="numOfDev">Number of Dev:</label>
      <input type="text" id="numOfDev" value="3" />
    </div>
    <div>
      <label for="numOfTester">Number Of Tester (not supported yet):</label>
      <input type="text" id="numOfTester" value="1" />
    </div>
    <div>
      <label>JIRA csv:</label>
      <input type="file" id="csvFileInput" multiple />
    </div>
  </div>
</template>