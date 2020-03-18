<template>
<div class="vue-papers">
  <div
    v-for="(page, pageIndex) in pages"
    :key="`page-index-${pageIndex}`"
    class="vue-papers__page"
    >

    <div
      class="vue-papers__page__space"
      :style="`height: ${paddingTop}px;`"
      />

    <div
      v-for="childIndex in page"
      :key="`child-index-${childIndex}`"
      class="vue-papers__page__child"
      >
      <div v-html="children[childIndex].outerHTML" />

      <div
        class="vue-papers__page__space"
        :style="`height: ${marginBetween}px`"
        />
    </div>

    <div
      class="vue-papers__page__space"
      :style="`height: ${paddingBottom}px;`"
      />
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
  @Prop({ default: 0 }) private paddingBottom!: number
  @Prop({ default: 0 }) private marginBetween!: number

  height = 1123 - 2 * 45 // Match print padding
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
    height -= this.paddingBottom
    height -= this.marginBetween

    return height
  }

  update () {
    this.$nextTick(() => {
      this.checkHeights()
    })
  }

  private getElementHeight (el: Element) {
    let height = el?.getBoundingClientRect().height

    const marginTop = parseInt(getComputedStyle(el, '').getPropertyValue('margin-top')) || 0
    const marginBottom = parseInt(getComputedStyle(el, '').getPropertyValue('margin-bottom')) || 0

    height += marginTop + marginBottom

    return height || 0
  }

  checkHeights () {
    let pageIndex = 0
    let pageHeight = 0
    const pages: number[][] = []
    const childrenHeights: Record<number, number> = {}

    for (let i = 0; i < this.children.length; i++) {
      const el = this.children[i] as Element
      let height = this.getElementHeight(el)

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
  @child-width: 704px; // 2x45 padding
  @print-side-padding: 45px;

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
      padding: @print-side-padding 0px;
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

      &__space {
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
