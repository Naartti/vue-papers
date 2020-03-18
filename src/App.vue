<template>
<div id="app">
  <vue-papers>
    <div
      v-for="(height, index) in blocks"
      :key="index"
      :style="`height: ${height}px;`"
      class="block"
      />
  </vue-papers>
</div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import VuePapers from './components/VuePapers.vue'

@Component({
  components: { VuePapers }
})
export default class App extends Vue {
  blocks = [120, 130, 140, 150, 140, 230, 220, 250, 120, 230, 110, 240, 150]
}
</script>

<style lang="less">
  .block {
    position: relative;
    background-color: skyblue;
    margin: 10px;
  }
</style>
