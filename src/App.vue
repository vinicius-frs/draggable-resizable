<template>
    <div style="width: 100vw; height: 100vh; margin: 0;">
      <div style="margin-bottom: 5px; margin-left: 15px;">
        <template v-if="altura > 0 && largura > 0">
          <button style="margin-top: 5px;" v-on:click="salvar()">Salvar</button>
          <button style="margin-left: 5px;" v-on:click="addQuadro()">Add quadro</button>
          <button style="margin-left: 5px;" v-on:click="removeQuadro()">Remover último quadro</button>
        </template>
        <template v-else>
          Resolução:
          <div>
            <label for="largura">Largura</label><input type="number" name="largura" id="largura" v-model="largura_aux">
            <label for="altura">Altura</label><input type="number" name="altura" id="altura" v-model="altura_aux">
            <button v-on:click="defRes()">Confirmar Resolução</button>
          </div>
        </template>
      </div>
      <div v-if="altura > 0 && largura > 0" id="div-ct" style="margin-left: 15px; background: linear-gradient(-90deg, rgba(0, 0, 0, 0.1) 1px, transparent 1px) 0% 0% / 640px 540px, linear-gradient(rgba(0, 0, 0, 0.1) 1px, transparent 1px) 0% 0% / 640px 540px; border: 1px solid red; position: relative; transform-origin: top left;" v-bind:style="'transform: scale(' + scaleX + ', ' + scaleY + '); height: ' + altura + 'px; width: ' + largura + 'px;'">
        <template v-for="quadro, idx_q in json_quadros">
          <vue-draggable-resizable :w="640" :h="540" @dragging="onDrag" @resizing="onResize" v-bind:parent="!(altura == 2160 && largura == 5760)" @resizestop="resizestop(idx_q, x, y, width, height)" :scale="[scaleX, scaleY]" v-bind:key=quadro.nome v-bind:id=quadro.nome>
            <div id="inputs_links">
              <p class="p_link">Link1<input type="text" style="border: 1px solid black" name="link1" id="link" link="1" v-bind:div="quadro.nome"></p>
              <p class="p_link">Link2<input type="text" style="border: 1px solid black" name="link2" id="link" link="2" v-bind:div="quadro.nome"></p>
            </div>
          </vue-draggable-resizable>
        </template>
        <!-- <template v-if="altura == 2160 && largura == 5760">
          <div id="div_grid_tvs" style="height: 100%; display: grid; box-sizing: border-box; grid-template-rows: 1fr 1fr;">
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
        </template> -->
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
    this.altura = 1080;
    this.largura = 1920;
    this.scaleX = ((Math.floor((document.body.offsetWidth/this.largura)*100)/100).toFixed(2)) - 0.02;
    this.scaleY = ((Math.floor((document.body.offsetHeight/this.altura)*100)/100).toFixed(2)) - 0.04;
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
      this.json_quadros.push({nome: "Q"+(this.json_quadros.length+1), x: 0, y: 0, width: 0, height: 0});
    },
    removeQuadro(){
      if(this.json_quadros.length > 1)
        this.json_quadros.splice(this.json_quadros.length-1, 1);
    },
    salvar(){
      // Salva html
      let html = document.getElementById("div-ct").innerHTML;
      localStorage.setItem("div-ct", html);

      // Salva links dos divs
      let arrLinks = [];
      let arrLinks_aux = [];
      document.querySelectorAll('[id^="link"]').forEach(element =>{
        if(element.getAttribute("link") == 1){
          let obj_link = {link1: "", link2: "", div: ""};
          obj_link.link1 = element.value;
          obj_link.div = element.getAttribute("div");
          arrLinks_aux[element.getAttribute("div")] = obj_link;
        }
        if(element.getAttribute("link") == 2){
          arrLinks_aux[element.getAttribute("div")].link2 = element.value;
        }
      });
      Object.entries(arrLinks_aux).forEach(element => {
        arrLinks.push(element[1]);
      });
      // let arrLinks2 = [];
      // document.querySelectorAll('[id^="link2"]').forEach(element =>{
      //     arrLinks2.push({link: element.value, div: element.getAttribute("div")});
      // });
      localStorage.setItem("div-ct-links", JSON.stringify(arrLinks));
      // localStorage.setItem("div-ct-link2", JSON.stringify(arrLinks2));
      localStorage.setItem("res-ori", JSON.stringify({largura: this.largura, altura: this.altura}));
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
