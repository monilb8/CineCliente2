<template>
	<div id="form">
		<div v-if="activo" class="detalle">
			<h2>Detalle Pelicula</h2>
			<div>
				<label>Título:</label>
				<input type="text" class="form-control" id="titulo" v-model="tituloPelicula" maxlength="30"/>
			</div>
			<div>
				<label>Director:</label>
				<input type="text" class="form-control" id="director" v-model="directorPelicula" maxlength="30"/>
			</div>
			<div>
				<label>Género:</label>
				<input type="text" class="form-control" id="genero" v-model="generoPelicula" maxlength="10"/>
			</div>
			<div>
				<label>País:</label>
				<input type="text" class="form-control" id="pais" v-model="paisPelicula" maxlength="20" />
			</div>
			<div class="botones">
				<input type="button" class="btn btn-outline-success btn-sm" id="btnEnv" value="Enviar" v-on:click="enviar"/>
				<input type="button" class="btn btn-outline-success btn-sm" id="btnAct" value="Actualizar" v-on:click="actualizar"/>
				<input type="button" class="btn btn-outline-success btn-sm" id="btnEli" value="Eliminar" v-on:click="eliminar"/>
				<input type="button" class="btn btn-outline-success btn-sm" id="btnVac" value="Vaciar" v-on:click="nuevo"/>
			</div>

			
		</div>
	</div>
</template>

<script>
  	import axios from 'axios';
  	import {EventBus} from './EventBus.js'
	export default {
		name: 'app',
		data () {
			return {
		    	pelicula:{},
		     	activo: true,
		     	tituloPelicula:undefined,
		     	directorPelicula:undefined,
		     	generoPelicula:undefined,
		     	paisPelicula:undefined,
		     	identificador:undefined
		    }
		},
		methods:{
		  	nuevo: function(){
		  		this.pelicula = {};
		  		this.tituloPelicula=undefined;
		     	this.directorPelicula=undefined;
		     	this.generoPelicula=undefined;
		     	this.paisPelicula=undefined;
		     	this.identificador=-1;

		  	},

		  	enviar: function(){
		    	if(this.identificador <0){
		    		let mensajeValidacion = isFormularioValido(this.tituloPelicula,this.directorPelicula,this.generoPelicula,this.paisPelicula);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Titulo: this.tituloPelicula,
					        Director: this.directorPelicula,
					        Genero: this.generoPelicula,
					        Pais: this.paisPelicula,
					        Id: this.identificador
				      	}
				        axios.post('http://10.0.2.2:62270/api/Pelicula/' ,data)
				        .then(response => {
				        	this.identificador = response.data.Id;
				          	EventBus.$emit("updatePelicula", this.data);
				        })
				        this.nuevo();
			        }else{
			        	alert(mensajeValidacion);
			        }
		    	}else{
		    		alert('Debes vaciar y volver a rellenar el formulario para insertar.');
		    	}
			},

			actualizar: function(){
		    	if(this.identificador >0){
		    		let mensajeValidacion = isFormularioValido(this.tituloPelicula,this.directorPelicula,this.generoPelicula,this.paisPelicula);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Titulo: this.tituloPelicula,
					        Director: this.directorPelicula,
					        Genero: this.generoPelicula,
					        Pais: this.paisPelicula,
					        Id: this.identificador
				      	}
						axios.put('http://10.0.2.2:62270/api/Pelicula/' + data.Id, data)
						.then(response => {
							EventBus.$emit("updatePelicula", this.data);
						})
						this.nuevo();
					}else{
			        	alert(mensajeValidacion);
			        }
				}else{
		    		alert('Debes seleccionar una pelicula para poder actualizar.');
		    	}
			},

			eliminar: function(){
		    	if(this.identificador >0){
			    	axios.delete('http://10.0.2.2:62270/api/Pelicula/' + this.identificador)
			        .then(response=> {
			          EventBus.$emit("updatePelicula", response.data);
			        })
					this.nuevo();
				}else{
		    		alert('Debes seleccionar una pelicula para poder borrar.');
		    	}
			}
		},

/*		computed:{
			nombre: function(){
				if(this.tituloPelicula > 10){
					alert('titulo largo');
				}
			}
		},*/

		created() {
    		this.pelicula = this.$parent.pelicula;
    		this.tituloPelicula = this.pelicula.Titulo;
    		this.directorPelicula= this.pelicula.Director;
		    this.generoPelicula= this.pelicula.Genero;
		    this.paisPelicula= this.pelicula.Pais;
		    this.identificador=this.pelicula.Id;
  		}
	}

	function isFormularioValido(titPelicula,dirPelicula,genPelicula,paPelicula){
		if(titPelicula == null || titPelicula=='' || (titPelicula!=null && titPelicula.trim()=='')){
			return 'El título de la pelicula debe estar relleno.'
		}
		if(dirPelicula == null || dirPelicula=='' || (dirPelicula!=null && dirPelicula.trim()=='')){
			return 'El director de la pelicula debe estar relleno.'
		}
		if(genPelicula == null || genPelicula=='' || (genPelicula!=null && genPelicula.trim()=='')){
			return 'El género debe estar relleno.'
		}
		if(paPelicula == null || paPelicula=='' || (paPelicula!=null && paPelicula.trim()=='')){
			return 'El país debe estar relleno.'
		}
		return '';
	}
</script>

<style>
	.detalle{
		width: 400px;
		margin-top: 5%;	
		margin-bottom: 5%;
	}
	.botones{
		margin-top: 5%;	
	}
</style>