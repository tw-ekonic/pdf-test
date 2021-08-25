<template>
  <div class="pdf-container">
    <div class="pdf-list-container" v-if="$data.pdf">
      <div  v-for="i in $data.pages" :key="i">
        <div v-if="!toBeDeleted.includes(i)" class="pdf-hover">
          <button v-on:click="hidePage(i)" class="delete">Delete</button>
          <button v-on:click="rotatePDF(i)" class="rotate">Rotate</button>
          <pdf class="pdf" :src="$data.renderedPdf" :page="i"> </pdf>
        </div>
      </div>
    </div>
    <button v-on:click="revertHide">Undo Changes</button>
    <button v-on:click="removePages">Save Changes</button>
  </div>
</template>

<script>
import { PDFDocument, degrees } from 'pdf-lib';

import pdf from 'vue-pdf';

export default {
  name: 'HelloWorld',
  data() {
    return {
      pdf: undefined,
      renderedPdf: '',
      pages: 0,
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
      this.toBeDeleted.forEach(index => this.pdf.removePage(index - 1));
      console.log(this.pdf.getPageCount());
      this.toBeDeleted = [];
        this.$nextTick(async () => {
          const rawPdf = await this.pdf.save();
          const doc = await PDFDocument.load(rawPdf);
          this.pdf = doc;
          this.renderedPdf = await doc.save();
          this.pages = doc.getPageCount();
        });
    },
    hidePage(i) {
      if (this.toBeDeleted.length + 1 !== this.pages ) {
        this.toBeDeleted.push(i);
      }
    },
    revertHide() {
      const lastIndex = this.toBeDeleted.length - 1;
      this.toBeDeleted.splice(lastIndex, 1);
    },
    rotatePDF(i) {
      const page = this.pdf.getPage(i - 1);
      const currentRotation = page.getRotation().angle;
      if (currentRotation === 0) {
        page.setRotation(degrees(90));
      } else if (currentRotation === 90) {
        page.setRotation(degrees(180));
      } else if (currentRotation === 180) {
        page.setRotation(degrees(270));
      } else if (currentRotation === 270) {
        page.setRotation(degrees(0));
      }

      this.$nextTick(async () => {
/*        const rawPdf = await this.pdf.save();*/
/*        const doc = await PDFDocument.load(rawPdf);
        this.pdf = doc;*/
        this.renderedPdf = await this.pdf.save();
/*        this.pages = doc.getPageCount();*/
      });
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
.rotate {
  position: absolute;
  right: 10px;
  top: 10px;
}
</style>
