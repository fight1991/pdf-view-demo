# pdf-view-demo
基于pdf.js的渲染
## 1. npm i pdfjs-dist

## 2. 在组件中或main.js中引入文件

    > import PDFJS from 'pdfjs-dist'
    > import { TextLayerBuilder } from 'pdfjs-dist/web/pdf_viewer'
    > import 'pdfjs-dist/web/pdf_viewer.css'
    > PDFJS.GlobalWorkerOptions.workerSrc = 'pdfjs-dist/build/pdf.worker.js' (为了提升解析和渲染PDF的性能)