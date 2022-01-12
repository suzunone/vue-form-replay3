<script>
import { defineComponent } from 'vue';
// Uncomment import and local "components" registration if library is not registered globally.
// import { VueFormReplay3Sample } from '@/entry.esm';

import "codemirror/lib/codemirror.css";
import "codemirror/mode/css/css.js";
import 'codemirror/theme/idea.css';
export default defineComponent({
  name: 'ServeDev',
  // components: {
  //  VueFormReplay3Sample,
  // }
});
</script>

<template>
  <div id="app">
    <vue-form-replay3-sample />
  </div>
</template>
