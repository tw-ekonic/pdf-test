<template>
    <div v-if="!toBeDeleted.includes(index)" class="pdf-hover">
      <div class="controls">
        <button v-on:click="hidePage(index)">Delete</button>
        <button v-on:click="rotate(index, -90)">Rotate left</button>
        <button v-on:click="rotate(index, +90)">
          Rotate right
        </button>
      </div>
      <div>
        <pdf
            class="pdf"
            :src="renderedPdf"
            :page="page"
            :style="{ transform: `rotate(${toBeRotated[index]}deg)` }"
        ></pdf>
      </div>
      <div>Page: {{ index + 1 }}</div>
    </div>
</template>

<script>
import pdf from 'vue-pdf';
import Vue from "vue";

export default {
  name: 'pdfPage',
  props: ['toBeDeleted', 'index', 'hidePage', 'toBeRotated', 'renderedPdf', 'page'],
  methods: {
    rotate(i, deg) {
      const rotation = this.toBeRotated[i];
      const newRotation = (rotation + deg) % 360;
      Vue.set(this.toBeRotated, i, newRotation);
      console.log(this.toBeRotated);
    },
  },
  components: {
    pdf,
  }
}
</script>

<style scoped>

.pdf {
  margin: 60px;
  width: 150px;
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
