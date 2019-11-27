<template>
  <div class="pdf-view" v-loading="loading">
    <div id="container"></div>
  </div>
</template>

<script>
import PDFJS from 'pdfjs-dist'
import { TextLayerBuilder } from 'pdfjs-dist/web/pdf_viewer'
import 'pdfjs-dist/web/pdf_viewer.css'
PDFJS.GlobalWorkerOptions.workerSrc = 'pdfjs-dist/build/pdf.worker.js'
let container = null
export default {
  name: 'pdf-view-demo',
  data () {
    return {
      loading: false
    }
  },
  props: {
    url: {
      type: String
    }
  },
  mounted () {
    this.$nextTick(() => {
      let url = 'https://test.5itrade.cn/files/longshine/document/ocr/upload/e48b9baa-5fe0-4d75-b638-4b2dbda078b5.pdf'
      this.getPDF(url)
      // this.getPDF(this.url)
    })
  },
  methods: {
    async getPDF (url) {
      this.loading = true
      let pdf = await PDFJS.getDocument(url)
      container = document.querySelector('#container')
      for (let i = 1; i <= pdf.numPages; i++) {
        try {
          await this.rendPDF(pdf, i)
          this.loading = false
        } catch (e) {
          this.$message.error(`第${i}页预览失败`)
          this.loading = false
        }
      }
    },
    async rendPDF (pdf, num) {
      let page = await pdf.getPage(num)
      // 设置展示比例
      let scale = 1.5
      let viewport = page.getViewport(scale)
      let pageDiv = document.createElement('div')
      pageDiv.setAttribute('id', 'page-' + (page.pageIndex + 1))
      pageDiv.setAttribute('style', 'position: relative;display:flex;justify-content:center;margin-bottom:5px;')
      container.appendChild(pageDiv)
      let canvas = document.createElement('canvas')
      pageDiv.appendChild(canvas)
      let context = canvas.getContext('2d')
      canvas.height = viewport.height
      canvas.width = viewport.width
      let renderContext = {
        canvasContext: context,
        viewport: viewport
      }
      await page.render(renderContext)
      let textContent = await page.getTextContent()
      // 创建文本图层div
      const textLayerDiv = document.createElement('div')
      textLayerDiv.setAttribute('class', 'textLayer')
      textLayerDiv.setAttribute('style', `width: ${viewport.width}px; margin: 0 auto`)
      // 将文本图层div添加至每页pdf的div中
      pageDiv.appendChild(textLayerDiv)
      // 创建新的TextLayerBuilder实例
      let textLayer = new TextLayerBuilder({
        textLayerDiv: textLayerDiv,
        pageIndex: page.pageIndex,
        viewport: viewport
      })
      textLayer.setTextContent(textContent)
      textLayer.render()
    }
  }
}
</script>
<style scoped lang="less">
.pdf-view {
  height: 100%;
  overflow-y: auto;
}

</style>
