<template>
    <div style="width: 100vw; height: 100vh; margin: 0;">
      <div>
        <template v-if="altura > 0 && largura > 0">
          <button v-on:click="salvar()">Salvar</button>
          <button v-on:click="addQuadro()">Add quadro</button>
          <button v-on:click="removeQuadro()">Remover último quadro</button>
        </template>
        <template v-else>
          Resolução:
          <div>
            <label for="altura">Altura</label><input type="number" name="altura" id="altura" v-model="altura_aux">
            <label for="largura">Largura</label><input type="number" name="largura" id="largura" v-model="largura_aux">
            <button v-on:click="defRes()">Salvar Resolução</button>
          </div>
        </template>
      </div>
      <div v-if="altura > 0 && largura > 0" id="div-ct" style="border: 1px solid red; position: relative; transform-origin: top left;" v-bind:style="'transform: scale(' + scaleX + ', ' + scaleY + '); height: ' + altura + 'px; width: ' + largura + 'px;'">
        <template v-for="quadro, idx_q in json_quadros">
          <vue-draggable-resizable :w="400" :h="400" @dragging="onDrag" @resizing="onResize" @resizestop="resizestop(idx_q, x, y, width, height)" :scale="[scaleX, scaleY]" v-bind:key=quadro.nome>
            <p style="font-size: 2em">X: {{ quadro.x }} / Y: {{ quadro.y }} - Width: {{ quadro.width }} / Height: {{ quadro.height }}</p>
          </vue-draggable-resizable>
        </template>
        <template v-if="altura == 2160 && largura == 5760">
          <div style="height: 100%; display: grid; box-sizing: border-box; grid-template-rows: 1fr 1fr;">
            <div style="display: grid; box-sizing: border-box; grid-template-columns: 1fr 1fr 1fr;">
              <div style="border: 1px solid black"></div>
              <div style="border: 1px solid black"></div>
              <div style="border: 1px solid black"></div>
            </div>
            <div style="display: grid; box-sizing: border-box; grid-template-columns: 1fr 1fr 1fr;">
              <div style="border: 1px solid black"></div>
              <div style="border: 1px solid black"></div>
              <div style="border: 1px solid black"></div>
            </div>
          </div>
        </template>
      </div>
    </div>
</template>

<script>
import VueDraggableResizable from 'vue-draggable-resizable'

export default {
  name: 'App',
  components: {
    VueDraggableResizable
  },
  data: function () {
    return {
      width: 0,
      height: 0,
      x: 0,
      y: 0,
      altura: 0,
      largura: 0,
      altura_aux: 0,
      largura_aux: 0,
      scaleX: 1,
      scaleY: 1,
      json_quadros: [
        {nome: "Q1", x: 0, y: 0, width: 0, height: 0}
      ]
    }
  },
  mounted() {
  },
  methods: {
    onResize: function (x, y, width, height) {
      this.x = x
      this.y = y
      this.width = width
      this.height = height
    },
    onDrag: function (x, y) {
      this.x = x
      this.y = y
    },
    resizestop(idx_q, x, y, width, height){
      this.json_quadros[idx_q].x = x;
      this.json_quadros[idx_q].y = y;
      this.json_quadros[idx_q].width = width;
      this.json_quadros[idx_q].height = height;
    },
    addQuadro(){
      this.json_quadros.push({nome: "Q"+this.json_quadros.length+1, x: 0, y: 0, width: 0, height: 0});
    },
    removeQuadro(){
      if(this.json_quadros.length > 1)
        this.json_quadros.splice(this.json_quadros.length-1, 1);
    },
    salvar(){
      let html = document.getElementById("div-ct").innerHTML;
      localStorage.setItem("div-ct", html);
    },
    defRes(){
      this.altura = this.altura_aux;
      this.largura = this.largura_aux;
      this.scaleX = ((Math.floor((document.body.offsetWidth/this.largura)*100)/100).toFixed(2)) - 0.02;
      this.scaleY = ((Math.floor((document.body.offsetHeight/this.altura)*100)/100).toFixed(2)) - 0.02;
    }
  }
}
</script>
