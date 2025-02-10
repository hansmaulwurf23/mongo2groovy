<script setup>
import {ref} from "vue";

const query = ref("")
const groovy = ref("")

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
  <div id="container">
    <textarea v-model="query" placeholder="JSON" @change="convert()"></textarea>
    <!--  <div class="btn btn-secondary m-3" @click="convert()">&downarrow;&downarrow;&downarrow; Convert to Groovy &downarrow;&downarrow;&downarrow;</div>-->
    <textarea v-model="groovy" placeholder="groovy" readonly></textarea>
  </div>
</template>

<style scoped>
#container {
  display: flex;
  flex-direction: column;
  padding: 2rem;
  height: 100vh;
}

textarea {
  resize: none;
  font-family: monospace;
  flex-grow: 1;
}

@media (min-width: 800px) {
  #container {
    flex-direction: row;
  }
}
</style>
