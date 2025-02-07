<script setup>
import {ref} from "vue";

const query = ref("{}")
const groovy = ref("[:]")

function convert() {
  let output = query.value.toString();
  // replace number literals
  output = output.replace(/NumberInt\((\d+)\)/gm, "$1");
  // replace date literals
  output = output.replace(/ISODate\(.*\)/gm, "new Date()");
  // replace double quoted strings with single ones to avoid parsing GStrings
  output = output.replace(/"/g, "'");
  // replace curly brackets with square ones
  output = output.replace(/{/g, "[");
  output = output.replace(/}/g, "]");
  groovy.value = output;
}
</script>

<template>
  <h1>JSON MongoDB Query</h1>
  <textarea v-model="query"></textarea>
  <div class="btn btn-secondary m-3" @click="convert()">&downarrow;&downarrow;&downarrow; Convert to Groovy &downarrow;&downarrow;&downarrow;</div>
  <textarea v-model="groovy"></textarea>
</template>

<style scoped>
textarea {
  min-width: 90%;
  max-width: 800px;
  min-height: 40vh;
  font-family: monospace;
}
</style>
