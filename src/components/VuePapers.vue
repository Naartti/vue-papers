<template>
<div class="vue-papers">
  <div
    v-for="(page, pageIndex) in pages"
    :key="`page-index-${pageIndex}`"
    class="vue-papers__page"
    >

    <div
      class="vue-papers__page__padding-top"
      :style="`height: ${paddingTop}px;`"
      />

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
export default class VuePapers extends Vue {
  @Prop({ default: 0 }) private paddingTop!: number
  height = 1123 - 2 * 40 // Match print padding
  width = 794
  pages: number[][] = []

  mounted () {
    this.update()
  }

  get children () {
    return (this.$refs.childrenWrapper as Element)?.childNodes || []
  }

  get availablePageHeight () {
    let height = this.height

    height -= this.paddingTop

    return height
  }

  update () {
    this.$nextTick(() => {
      this.checkHeights()
    })
  }

  checkHeights () {
    let pageIndex = 0
    let pageHeight = 0
    const pages: number[][] = []
    const childrenHeights: Record<number, number> = {}

    for (let i = 0; i < this.children.length; i++) {
      const el = this.children[i] as Element
      let height = el?.getBoundingClientRect().height

      const margin = parseInt(getComputedStyle(el, '').getPropertyValue('margin-top')) +
        parseInt(getComputedStyle(el, '').getPropertyValue('margin-bottom'))

      console.log(margin)
      height += margin

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
  @child-width: 714px; // 2x40 padding

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
      position: relative;
      width: @page-width;
      min-height: @page-height;
      height: @page-height;
      max-height: @page-height;
      box-shadow: @shadow;
      background-color: #ffffff;
      box-sizing: border-box;
      margin-bottom: 20px;
      padding: 40px 0px; // NOTE: Keep 40px, it's for matching print.
      break-inside: avoid;

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
        box-sizing: border-box;
        width: @child-width;
        margin: 0px auto;

        @media print {
          overflow: initial;
          break-inside: avoid;
          filter: none;
        }
      }

      &__padding-top {
        position: relative;
        background-color: pink;
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
