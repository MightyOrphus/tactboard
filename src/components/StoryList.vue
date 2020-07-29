<template>
  <table id="storyList">
    <tr>
      <th>Stories</th>
    </tr>
    <template v-for="story in stories">
      <tr v-bind:key="story.issueId">
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