<template>
  <div id="home" class="contenedor">
    <div id="pelicula" class="menu" v-on:click="abrirComponente">
      Pelicula
    </div>
    <div id="entrada" class="menu" v-on:click="abrirComponente">
      Entrada
    </div>

    <div id="maestro"></div>
  </div>
</template>

<script>
  import axios from "axios"
  import Vue from 'vue'
  import MasterPelicula from "./MaestroPelicula.vue"
  import MasterEntrada from "./MaestroEntrada.vue"

  export default {
    name: 'app',
    data () {
      return {
      }
    },
    methods:{
      abrirComponente:function(e){
        switch(e.target.id){
          case "pelicula":
            new Vue({
              el: '#maestro',
              render: h => h(MasterPelicula)
            })
            break;

          case "entrada":
            new Vue({
              el:'#maestro',
              render: h => h(MasterEntrada)
            })
            break;
        }

      }
    }
  }
</script>

<style>
  .contenedor{
    margin: 2%;
  }
  .menu {
    padding: 5px;
    display:inline-block;
    width: 33%;
    text-align: center;
    font-weight: bold;
    color: white;
    background-color: #61AC6C; 
    border-radius: 5px;
    border: 2px solid #777777;
    margin-top: 5px;
  }

</style>