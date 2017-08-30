<template>
	<div id="maestro" class="contenedor">
		<h2 class="titulo">Entradas</h2>
		<table v-if="entradas && entradas.length" class="table table-bordered">
		 	<tr>
		 		<th>Pelicula</th>
		 		<th>Duración</th>
		 		<th>Sala</th>
		 	</tr>
			<tr v-for="entrada of entradas" v-on:click="detalle" v-bind:id="entrada.Id">
				<td>{{ entrada.Pelicula }}</td>
				<td>{{ entrada.Duracion }}</td>
				<td>{{ entrada.Sala }}</td>
			</tr>
		</table>
		<input type="button" class="btn btn-success btn-sm" id="nuevo" value="Mostrar Detalle" v-on:click="nuevo"/>
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
<style>
	.contenedor{
		width: 800px;
	}
	.titulo{
		margin-top: 3%;
	}
	
</style>

