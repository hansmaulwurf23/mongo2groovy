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

function pp() {
  let output = query.value.toString();
  // replace number literals
  output = output.replace(/NumberInt\((\d+)\)/gm, '"___NumberInt___$1"');
  // replace date literals
  output = output.replace(/ISODate\("(.*)"\)/gm, '"___ISODate___$1"');//'"__ISODate__"$1');
  let j = JSON.parse(output);
  query.value = JSON.stringify(j, (k, v) => {
    if (typeof v === 'string') {
      if (v.startsWith('___NumberInt___')) {
        v = 'NumberInt(' + v.substring(15, v.length) + ')';
      } else if (v.startsWith('___ISODate___')) {
        v = 'ISODate(' + v.substring(13, v.length) + ')';
      }
    }
    return v
  }, 2);

  convert();
}

function syncscroll(event) {
  let st = event.target.scrollTop;
  Array.from(document.getElementsByTagName("textarea")).forEach((el) => {
    el.scrollTop = st;
  });
}
</script>

<template>
  <div id="container">
    <textarea v-model="query" @dblclick="pp()" @scroll="syncscroll($event)" placeholder="JSON" @change="convert()"></textarea>
    <textarea v-model="groovy" @scroll="syncscroll($event)" placeholder="groovy" readonly></textarea>
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
