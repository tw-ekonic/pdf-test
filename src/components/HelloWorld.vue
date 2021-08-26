<template>
  <div class="pdf-container">
    <div class="pdf-list-container" v-if="$data.pagesCount">
      <pdf-page :hidePage="hidePage"
                :index="index"
                :toBeDeleted="toBeDeleted"
                :toBeRotated="toBeRotated"
                :renderedPdf="$data.renderedPdf"
                v-for="(n, index) in $data.pagesCount"
                :n="n"
                :key="index"/>
    </div>
    <button v-on:click="revertHide">Undo Changes</button>
    <button v-on:click="saveChanges">Save Changes</button>
  </div>
</template>

<script>
import { PDFDocument, degrees } from 'pdf-lib';

import pdfPage from './pdf-page'
import Vue from 'vue';

export default {
  name: 'HelloWorld',
  data() {
    return {
      pdf: PDFDocument,
      renderedPdf: Uint8Array,
      pagesCount: Number,
      toBeDeleted: [],
      toBeRotated: [],
    };
  },
  async mounted() {
    const url = 'test.pdf';
    const arrayBuffer = await fetch(url).then((res) => res.arrayBuffer());
    const doc = await PDFDocument.load(arrayBuffer);

    doc.getPages().forEach((page) => {
      this.toBeRotated.push(page.getRotation().angle);
    });

    this.pdf = doc;
    this.renderedPdf = await doc.save();
    this.pagesCount = doc.getPageCount();
  },
  methods: {
    async saveChanges() {
      this.toBeDeleted.forEach((index) => this.pdf.removePage(index));
      this.toBeDeleted = [];

      this.toBeRotated.forEach((value, index) => {
        const currentRotation = parseInt(
          this.pdf.getPage(index).getRotation().angle
        );

        this.pdf
          .getPage(index)
          .setRotation(degrees(currentRotation + parseInt(value)));
      });
      this.toBeRotated = [];

      this.$nextTick(async () => {
        const rawPdf = await this.pdf.save();
        const doc = await PDFDocument.load(rawPdf);

        doc.getPages().forEach((_, index) => {
          Vue.set(this.toBeRotated, index, 0);
        });

        this.pdf = doc;
        this.renderedPdf = await doc.save();
        this.pagesCount = doc.getPageCount();
      });
    },
    hidePage(i) {
      if (this.toBeDeleted.length + 1 !== this.pages) {
        this.toBeDeleted.push(i);
      }
    },
    revertHide() {
      const lastIndex = this.toBeDeleted.length - 1;
      this.toBeDeleted.splice(lastIndex, 1);
    },
  },
  components: {
    pdfPage
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pdf-list-container {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  width: 100%;
  justify-content: center;
}
</style>
