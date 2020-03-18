<template>
<div id="app">
  <vue-papers
    ref="vuePapers"
    :padding-top="120"
    >
    <div
      v-for="(height, index) in blocks"
      :key="index"
      :style="`height: ${height}px; margin: ${margin}px;`"
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
  margin = 0
  blocks = [120, 130, 140, 150, 140, 230, 220, 250, 120, 230, 110, 240, 150]

  mounted () {
    window.dev.setMargin = (margin: number) => {
      this.margin = margin
      console.log('New margin: ' + margin)

      const el = this.$refs.vuePapers as VuePapers
      el.update()
    }
  }
}
</script>

<style lang="less">
  .block {
    position: relative;
    background-color: skyblue;
    margin: 0px;
    border: 1px solid blue;
    box-sizing: border-box;
  }
</style>
