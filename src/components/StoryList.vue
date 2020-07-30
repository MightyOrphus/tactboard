<template>
  <table class="storyList">
    <tr>
      <th>Stories</th>
    </tr>
    <template v-for="story in stories">
      <tr v-bind:key="story.issueId" :style="{'background-color': story.color }">
        <template>
          <td>{{story.issueKey}}: {{story.summary}}</td>
        </template>
      </tr>
    </template>
  </table>
</template>

<script>
export default {
  props: {
    stories: Array,
  },
};
</script>
<style scoped>
.storyList {
  background-color: var(--backgrounds-3-hex);
  border-style: groove;
  border-color: black;
}
td {
  padding-left: 3px;
  font-size: 15px;
}
</style>