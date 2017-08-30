<template>
	<div id="maestro">
		<h2>Entradas</h2>
		<ul v-for="entrada of entradas" v-on:click="detalle" v-bind:id="entrada.Id">
			<li>{{ entrada.Pelicula }}</li>
		</ul>
		<input type="button" class="btn btn-primary" id="nuevo" value="Mostrar Detalle" v-on:click="nuevo"/>
		<div id="form"></div>
	</div>
</template>

<script>
  import axios from 'axios';
  import Formulario from './DetalleEntrada.vue'
  import {EventBus} from './EventBus.js';
  import Vue from 'vue'

	export default {
	  	name: 'app',
	  	data () {
	   		return {
	    		entradas: undefined
	    	}
	 	},
	 	methods:{
		  	inicio: function(){
		  		axios.get('http://10.0.2.2:62270/api/Entradas/')
			  		.then(response => {
			  			this.entradas = response.data;
		  			})
		  	},

		  	detalle: function(evt){
		  		let id = evt.target.id;
		  			id = evt.target.parentNode.id;
		  		this.entradas.forEach((e, index) =>{
		  			if(e.Id == id){
		  				new Vue({
		  					el: '#form',
		  					render: h => h(Formulario),
		  					data:{
		  						entrada: e
		  					},
		  				});
		  			}
		  		});
		  	},
		  	nuevo: function(evt){

				let ent = {};
		  		ent.Id=-1;
		  		ent.Pelicula=undefined;
		  		ent.Duracion=undefined;
		  		ent.Sala=undefined;

		  		new Vue({
				el: '#form',
				render: h => h(Formulario),
				data:{
					entrada:ent
				},
			});	
	  	}
  	},
  	created() {
      	this.inicio();
    	EventBus.$on('updateEntrada', (() => {
        	this.inicio();
    	}));
    }
}
</script>

