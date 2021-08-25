<template>
  <div class="pdf-container">
    <div class="pdf-list-container" v-if="$data.pagesCount">
      <div v-for="index in $data.pagesCount" :key="index">
        <div v-if="!toBeDeleted.includes(index)" class="pdf-hover">
          <div class="controls">
            <button v-on:click="hidePage(index)">Delete</button>
            <button v-on:click="rotateRight($event, index)">
              Rotate right
            </button>
            <button v-on:click="rotateLeft($event, index)">Rotate left</button>
          </div>
          <div>
            <pdf
              class="pdf"
              :src="$data.renderedPdf"
              :page="index"
              data-rotation="0"
            ></pdf>
          </div>
          <div>Page: {{ index }}</div>
        </div>
      </div>
    </div>
    <button v-on:click="revertHide">Undo Changes</button>
    <button v-on:click="saveChanges">Save Changes</button>
  </div>
</template>

<script>
import { PDFDocument, degrees } from 'pdf-lib';

import pdf from 'vue-pdf';

export default {
  name: 'HelloWorld',
  data() {
    return {
      pdf: PDFDocument,
      renderedPdf: Uint8Array,
      pagesCount: Number,
      toBeDeleted: [],
      toBeRotated: new Map(),
    };
  },
  async mounted() {
    const url = 'test.pdf';
    const arrayBuffer = await fetch(url).then((res) => res.arrayBuffer());
    const doc = await PDFDocument.load(arrayBuffer);
    this.pdf = doc;
    this.renderedPdf = await doc.save();
    this.pagesCount = doc.getPageCount();
  },
  methods: {
    async saveChanges() {
      this.toBeDeleted.forEach((index) => this.pdf.removePage(index - 1));
      this.toBeDeleted = [];

      console.log(this.toBeRotated);
      this.toBeRotated.forEach((value, key) => {
        key -= key - 1;
        console.log(value);
        console.log(key);
        const currentRotation = parseInt(
          this.pdf.getPage(key).getRotation().angle
        );
        this.pdf.getPage(key).setRotation(degrees(currentRotation + value));
      });
      this.toBeRotated = new Map();

      this.$nextTick(async () => {
        const rawPdf = await this.pdf.save();
        const doc = await PDFDocument.load(rawPdf);
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
    rotateLeft(event, i) {
      console.log(i);
      const attribute = 'data-rotation';
      const element = event.target.parentNode.nextSibling.firstChild;
      const rotation = parseInt(element.getAttribute(attribute));
      const newRotation = (rotation - 90) % 360;

      element.setAttribute(attribute, newRotation);
      element.style.transform = `rotate(${newRotation}deg)`;

      this.toBeRotated.set(i, newRotation);
    },
    rotateRight(event, i) {
      console.log(i);
      const attribute = 'data-rotation';
      const element = event.target.parentNode.nextSibling.firstChild;
      const rotation = parseInt(element.getAttribute(attribute));
      const newRotation = (rotation + 90) % 360;

      element.setAttribute(attribute, newRotation);
      element.style.transform = `rotate(${newRotation}deg)`;
      this.toBeRotated.set(i, newRotation);
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
  flex-direction: column;
}

.pdf-hover:hover .controls {
  display: flex;
}

.pdf-hover:hover {
  position: relative;
  background: #ebebeb;
}

.controls {
  display: none;
  position: absolute;
}
</style>
