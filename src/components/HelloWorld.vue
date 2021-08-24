<template>
  <div class="pdf-container">
    <div class="pdf-list-container" v-if="$data.pdf">
      <div class="pdf-hover" v-for="i in getLength" :key="i" >
        <button v-on:click="removePage(i)" class="delete">Delete</button>
        <pdf class="pdf"
             :page="i"
             :src="renderPdf()">
        </pdf>
      </div>
    </div>
  </div>
</template>

<script>

import {PDFDocument} from 'pdf-lib'

import pdf from 'vue-pdf';

export default {
  name: 'HelloWorld',
  methods: {
    async removePage(i) {
      await this.$data.pdf.removePage(i -  1);
      this.$data.pdf = await this.$data.pdf.saveAsBase64({dataUri: true});
      console.log(i);
      this.$data.pdf = await this.$data.pdf.saveAsBase64({dataUri: true});
    }
  },
  computed: {
    getLength() {
      console.log(this.$data.pdf);
      return this.$data.pdf.getPages().length;
    },
    async renderPdf() {
      return await this.$data.pdf.saveAsBase64({dataUri: true});
    }
  },
  data() {
    return {
      pdf: undefined
    }
  },
  async mounted() {
    const url = 'test.pdf'
    const arrayBuffer = await fetch(url).then(res => res.arrayBuffer())
    this.$data.pdf = await PDFDocument.load(arrayBuffer);
  },
  components: {
    pdf
  }
}
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
   background: #EBEBEB;
 }
 .delete {
   position: absolute;
   left: 10px;
   top: 10px;
 }
</style>
