<template>
  <div class="pdf-container">
    <div class="pdf-list-container" v-if="$data.pdf">
      <div  v-for="i in $data.pages" :key="i">
        <div v-if="!toBeDeleted.includes(i)" class="pdf-hover">
          <button v-on:click="hidePage(i)" class="delete">Delete</button>
          <pdf class="pdf" :src="$data.renderedPdf" :page="i"> </pdf>
        </div>
      </div>
    </div>
    <button v-on:click="removePages">Save Changes</button>
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
      toBeDeleted: [],
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
    async removePages() {
      this.toBeDeleted.forEach(index => this.$data.pdf.removePage(index - 1));
      this.toBeDeleted = [];
        this.$nextTick(async () => {
          const rawPdf = await this.pdf.save();
          const doc = await PDFDocument.load(rawPdf);
          this.renderedPdf = rawPdf;
          this.pages = doc.getPageCount();
        });
    },
    hidePage(i) {
      this.toBeDeleted.push(i);
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
.pdf-hover {
  height: 100%;
  display: flex;
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
