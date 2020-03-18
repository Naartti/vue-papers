<template>
<div class="vue-papers">
  <div
    v-for="(page, pageIndex) in pages"
    :key="`page-index-${pageIndex}`"
    class="vue-papers__page"
    >
    <div
      v-for="childIndex in page"
      :key="`child-index-${childIndex}`"
      class="vue-papers__page__child"
      v-html="children[childIndex].outerHTML"
      >
    </div>
  </div>

  <div
    class="vue-papers__dummy no-print"
    ref="childrenWrapper"
    >
    <slot />
  </div>
</div>
</template>
<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'

@Component
export default class HelloWorld extends Vue {
  height = 1123
  pages: number[][] = []

  mounted () {
    this.checkHeights()
  }

  get children () {
    return (this.$refs.childrenWrapper as Element)?.childNodes || []
  }

  get availablePageHeight () {
    return this.height
  }

  checkHeights () {
    let pageIndex = 0
    let pageHeight = 0
    const pages: number[][] = []
    const childrenHeights: Record<number, number> = {}

    for (let i = 0; i < this.children.length; i++) {
      const el = this.children[i] as Element
      let height = el?.getBoundingClientRect().height

      if (!height) {
        continue
      }

      height = Math.ceil(height)
      childrenHeights[i] = height

      pageHeight += height
      if (pageHeight > this.availablePageHeight) {
        pageHeight = height
        pageIndex++
      }

      if (!pages[pageIndex]) {
        pages[pageIndex] = []
      }

      pages[pageIndex].push(i)
    }

    console.log(childrenHeights)
    this.pages = pages
  }
}
</script>
<style scoped lang="less">
  @shadow: 0px 5px 15px rgba(0, 0, 0, 0.2);
  @page-width: 794px; // 21cm;
  @page-height: 1123px; // 29.7cm;

  .vue-papers {
    position: relative;

    @media print {
      margin: initial;
      overflow: initial;
      max-height: none;
      background-color: initial;

      &:last-child &__page {
        height: auto !important;
      }
    }

    &__page {
      width: @page-width;
      min-height: @page-height;
      height: @page-height;
      max-height: @page-height;
      box-shadow: @shadow;
      background-color: #ffffff;

      @media print {
        padding: initial;
        margin: initial;
        border-radius: initial;
        width: initial;
        min-height: initial;
        max-height: initial;
        height: 100vh;
        box-shadow: none;
        position: relative;
        overflow: initial;
        zoom: 1;
      }

      &__child {
        position: relative;

        @media print {
          overflow: initial;
          break-inside: avoid;
          filter: none;
        }
      }
    }

    /**
     * NOTE: This dummy is for calculating question height
     */
    &__dummy {
      display: block;
      opacity: 0;
      position: fixed;
      top: 0px;
      left: 100vw;
      width: @page-width;
      height: auto;
      min-height: 100vh;
    }
  }

  .no-print {
    @media print {
      display: none !important;
    }
  }
</style>
