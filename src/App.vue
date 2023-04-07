<template>
  <div id="app">

    <div class="urna">

      <Tela :tela="tela" 
      :numeroVoto="numeroVoto" 
      :quantidadeNumeros="quantidadeNumeros"
      :candidato="candidato"
       />
      <Teclado
      :adicionarNumero="adicionarNumero"
      :corrigir="corrigir"
      :confirmar="confirmar"
      :votarEmBranco="votarEmBranco"
       />

    </div>

  </div>
</template>

<script>

import "@/css/global.css";

// eslint-disable-next-line no-unused-vars
import Teclado from "./components/Teclado.vue";

// eslint-disable-next-line no-unused-vars
import Tela from "./components/Tela.vue";

import confirmAudio from '@/assets/confirm.wav'
import keyAudio from '@/assets/key.wav'

export default {
  name: 'App',
  components: {
    // eslint-disable-next-line vue/no-unused-components
    Teclado,
    // eslint-disable-next-line vue/no-unused-components
    Tela
  },
  methods:{
    adicionarNumero(numero){
      this.executarSom(keyAudio);
      //verificar limite de numeros
      if(this.numeroVoto.length == this.quantidadeNumeros){
        return false;
      }
      //add numero selecionado
      this.numeroVoto += ''+numero;

      //verificar candidato votado
      this.verificarCandidato();
    },
    verificarCandidato(){
      //voto incompleto
      if(this.numeroVoto.length < this.quantidadeNumeros){
        return false;
      }
      //verificar candidato existente
      if(this.candidatos[this.tela][this.numeroVoto]){
        this.candidato = this.candidatos[this.tela][this.numeroVoto];
        return true;
      }
      //voto nulo
      this.candidato = {
        nome: 'Voto nulo',
        partido: 'Voto nulo',
        imagem: ''
      }
    },
    corrigir(){
      this.limpar();
    },
    limpar(){
      this.candidato = {}
      this.numeroVoto = ''
    },
    confirmar(){
      //VOTO INCOMPLETO
      if(this.numeroVoto.length < this.quantidadeNumeros){
        return false;
      }

      return this.avancarTela();
    },
    avancarTela(){
      this.executarSom(confirmAudio);
      //VEREADOR
      if(this.tela == "prefeito"){
        this.tela = "vereador";
        this.quantidadeNumeros = 5;
        return this.limpar();
      }

      this.tela = 'fim';

      var instancia = this;

      setTimeout(function(){
        instancia.tela = "prefeito";
        instancia.quantidadeNumeros = 2;
        return instancia.limpar();
      },3000);
    },

    votarEmBranco(){
      if(this.tela == "fim") return false;

      this.limpar();
      this.avancarTela();
    },
    executarSom(arquivoSom){
      if(arquivoSom){
        let audio = new Audio(arquivoSom);
        audio.play();
      }
    }
  },
  data() {
    return {
      tela: "prefeito",
      numeroVoto: '',
      quantidadeNumeros: 2,
      candidato:{},
      candidatos: {
        "prefeito": {
          "01": {
            "nome": "Ash",
            "partido": "Pokemon",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/ash.png"
          },
          "08": {
            "nome": "Vegeta",
            "partido": "Dragon Ball",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/vegeta.png"
          }
        },
        "vereador": {
          "01234": {
            "nome": "Pikachu",
            "partido": "Pokemon",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/pikachu.png"
          },
          "08001": {
            "nome": "Goku",
            "partido": "Dragon Ball",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/goku.png"
          }
        }
      }
    }
  }
}
</script>

<style>
#app {
  background-color: var(--background-color);
  width: 100%;
  height: 100%;
  display: flex;
  ;
  justify-content: center;
  align-items: center;
}

.urna {
  width: 1000px;
  height: 500px;
  background-color: var(--ballot-box-background-color);
  padding: 30px;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
}
</style>
