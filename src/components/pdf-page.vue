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
            :page="n"
            :style="{ transform: `rotate(${toBeRotated[index]}deg)` }"
        ></pdf>
      </div>
      <div>Page: {{ index }}</div>
    </div>
</template>

<script>
import pdf from 'vue-pdf';

export default {
  name: 'pdfPage',
  props: ['toBeDeleted', 'index', 'hidePage', 'rotate', 'toBeRotated', 'renderedPdf', 'n'],
  components: {
    pdf,
  }
}
</script>

<style scoped>

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
