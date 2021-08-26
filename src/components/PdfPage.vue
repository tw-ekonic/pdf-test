<template>
  <div v-if="!toBeDeleted.includes()" class="pdf-hover">
    <div class="controls">
      <button v-on:click="hidePage()">Delete</button>
      <button v-on:click="rotate(-90)">Rotate left</button>
      <button v-on:click="rotate(+90)">
        Rotate right
      </button>
    </div>
    <div>
      <pdf
        class="pdf"
        :src="$data.renderedPdf"
        :page="n"
        :style="{ transform: `rotate(${rotation}deg)` }"
      ></pdf>
    </div>
    <div>Page: {{ index }}</div>
  </div>
</template>

<script>
import Pdf from 'vue-pdf';

export default {
  props: {
    initialRotation: {
      type: Number,
      default: 0,
      required: true,
    },
    initialPage: {
      type: Number,
      default: 0,
      required: true,
    },
  },
  data() {
    return {
      rotation: this.initialRotation,
      page: this.initialPage,
    };
  },
  methods: {
    hidePage() {
      // if (this.toBeDeleted.length + 1 !== this.pages) {
      //   this.toBeDeleted.push(i);
      // }
    },
    rotate(deg) {
      const newRotation = (this.rotation + deg) % 360;
      this.rotation = newRotation;
    },
  },
  components: {
    Pdf,
  },
};
</script>

<style>
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
