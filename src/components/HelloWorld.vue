<template>
  <div class="pdf-container">
    <div class="pdf-list-container" v-if="$data.pdf">
      <div class="pdf-hover" v-for="i in $data.pages" :key="i">
        <button v-on:click="removePage(i)" class="delete">Delete</button>
        <pdf class="pdf" :src="$data.renderedPdf" :page="i"> </pdf>
      </div>
    </div>
  </div>
</template>

<script>
import { PDFDocument } from 'pdf-lib';

import pdf from 'vue-pdf';

export default {
  name: 'HelloWorld',
  data() {
    return {
      pdf: undefined,
      renderedPdf: String,
      pages: Number,
    };
  },
  async mounted() {
    const url = 'test.pdf';
    const arrayBuffer = await fetch(url).then((res) => res.arrayBuffer());
    const doc = await PDFDocument.load(arrayBuffer);

    this.$data.pdf = doc;
    this.$data.renderedPdf = await doc.save();
    this.pages = doc.getPageCount();
  },
  methods: {
    async removePage(i) {
      if (i > 0 && this.$data.pages > i) {
        this.$data.pdf.removePage(i - 1);
        this.$nextTick(async () => {
          const rawPdf = await this.pdf.save();
          const doc = await PDFDocument.load(rawPdf);
          this.renderedPdf = rawPdf;
          this.pages = doc.getPageCount();
        });
      }
    },
  },
  components: {
    pdf,
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
.pdf {
  margin: 40px;
  width: 200px;
}
.pdf-hover:hover {
  position: relative;
  background: #ebebeb;
}
.delete {
  position: absolute;
  left: 10px;
  top: 10px;
}
</style>
