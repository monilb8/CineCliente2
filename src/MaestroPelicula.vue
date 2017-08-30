<template>
	<div id="maestro">
		<h2>Peliculas</h2>
		<ul v-for="pelicula of peliculas" v-on:click="detalle" v-bind:id="pelicula.Id">
			<li> {{ pelicula.Titulo }}</li>
		</ul>
		<input type="button" id="nuevo" value="Mostrar Detalle" v-on:click="nuevo"/>
		<div id="form"></div>
	</div>
</template>

<script>
  import axios from 'axios';
  import Formulario from './DetallePelicula.vue'
  import {EventBus} from './EventBus.js';
  import Vue from 'vue'

	export default {
	  	name: 'app',
	  	data () {
	   		return {
	    		peliculas: undefined
	    	}
	 	},
	 	methods:{
		  	inicio: function(){
		  		axios.get('http://10.0.2.2:62270/api/Pelicula/')
			  		.then(response => {
			  			this.peliculas = response.data;
		  			})
		  	},

		  	detalle: function(evt){
		  		let id = evt.target.id;
		  			id = evt.target.parentNode.id;
		  		this.peliculas.forEach((p, index) =>{
		  			if(p.Id == id){
		  				new Vue({
		  					el: '#form',
		  					render: h => h(Formulario),
		  					data:{
		  						pelicula: p
		  					},
		  				});
		  			}
		  		});
		  	},
		  	nuevo: function(evt){

				let peli = {};
		  		peli.Id=-1;
		  		peli.Titulo=undefined;
		  		peli.Director=undefined;
		  		peli.Genero=undefined;
		  		peli.Pais=undefined;

		  		new Vue({
				el: '#form',
				render: h => h(Formulario),
				data:{
					pelicula:peli
				},
			});	
	  	}
  	},
  	created() {
      	this.inicio();
    	EventBus.$on('updatePelicula', (() => {
        	this.inicio();
    	}));
    }
}
</script>