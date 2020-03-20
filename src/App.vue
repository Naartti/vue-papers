<template>
<div id="app"
  :class="{ scale }"
  >
  <button class="no-print" @click="scale = !scale">SCALE</button>

  <vue-papers
    ref="vuePapers"
    :padding-top="0"
    :padding-bottom="padding"
    :margin-between="0"
    :page-height="112"
    >

    <h3
      slot="header"
      contenteditable
      style="border: 1px solid black;"
      v-html="header"
      @blur="ev => header = ev.target.innerText"
      />
    <h5
      slot="footer"
      contenteditable
      style="border: 1px solid black;"
      v-html="footer"
      @blur="ev => footer = ev.target.innerText"
      />

    <div
      v-for="(height, index) in blocks"
      :key="index"
      :style="`height: ${height * multiplier}px; margin: ${margin}px;`"
      class="block"
      />
  </vue-papers>

  <vue-papers
    :page-height="112"
    >
    <h1 slot="header">Page 2</h1>

    <div
      v-for="(height, index) in blocks"
      :key="index"
      :style="`height: ${height * multiplier}px; margin: ${margin}px;`"
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
  header = 'header'
  footer = 'footer'
  margin = 5
  multiplier = 1
  padding = 0
  scale = true

  get blocks () {
    return [120, 130, 140, 150, 140, 230, 220, 250, 120, 230, 110, 240, 150]
      .map(a => a * this.multiplier)
  }

  mounted () {
    window.dev.setMultiplier = (multiplier: number) => {
      this.multiplier = multiplier
    }

    window.dev.setPadding = (padding: number) => {
      this.padding = padding
      this.update()
    }

    window.dev.setMargin = (margin: number) => {
      this.margin = margin
      this.update()
    }
  }

  update () {
    const el = this.$refs.vuePapers as VuePapers
    el.update()
  }
}
</script>
<style lang="less">
  body {
    margin: 0px;
    word-wrap: break-word;
    overflow: hidden;

    p {
      margin: 0px;
    }
  }

  .MJXc-display {
    display: inline-block !important;
  }

  #app {
    position: relative;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    position: relative;
    width: 100%;
    height: 100%;
  }

  .scale {
    transform: translateX(10px) translateY(10px) scale(0.1);
    transform-origin: 0 0;
    overflow-y: initial;

    @media print {
      transform: none;
    }
  }

  .block {
    position: relative;
    background-color: skyblue;
    margin: 0px;
    // border: 1px solid blue;
    box-sizing: border-box;
  }

  .no-print {
    @media print {
      display: none;
    }
  }
</style>
