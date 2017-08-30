<template>
	<div id="form">
		<div v-if="activo">
			<h2>Detalle Entrada</h2>
			<div>
				<label>Pelicula:</label>
				<input type="text" id="peli" v-model="nombrePelicula" maxlength="40"/>
			</div>
			<div>
				<label>Duración:</label>
				<input type="number" id="duracion" name="duracion" v-model="duracionPelicula"/>
			</div>
			<div>
				<label>Sala:</label>
				<input type="number" id="sala" name="sala" v-model="salaPelicula" />
			</div>

			<input type="button" id="btnEnv" value="Enviar" v-on:click="enviar"/>
			<input type="button" id="btnAct" value="Actualizar" v-on:click="actualizar"/>
			
			<input type="button" id="btnVac" value="Vaciar" v-on:click="nuevo"/>

			<button type="submit" class="btn btn-primary" v-on:click="eliminar"><span class="glyphicon glyphicon-remove"></span> Eliminar </button>


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
		    	entrada:{},
		     	activo: true,
		     	nombrePelicula:undefined,
		     	duracionPelicula:undefined,
		     	salaPelicula:undefined,
		     	identificador:undefined
		    }
		},
		methods:{
		  	nuevo: function(){
		  		this.entrada = {};
		  		this.nombrePelicula=undefined;
		     	this.duracionPelicula=undefined;
		     	this.salaPelicula=undefined;
		     	this.identificador=-1;
		  	},

		  	enviar: function(){
		    	if(this.identificador <0){
		    		let mensajeValidacion = isFormularioValido(this.nombrePelicula,this.duracionPelicula,this.salaPelicula);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Pelicula: this.nombrePelicula,
					        Duracion: this.duracionPelicula,
					        Sala: this.salaPelicula,
					        Id: this.identificador
				      	}
				        axios.post('http://10.0.2.2:62270/api/Entradas/' ,data)
				        .then(response => {
				        	this.identificador = response.data.Id;
				          	EventBus.$emit("updateEntrada", this.data);
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
		    		let mensajeValidacion = isFormularioValido(this.nombrePelicula,this.duracionPelicula,this.salaPelicula);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Pelicula: this.nombrePelicula,
					        Duracion: this.duracionPelicula,
					        Sala: this.salaPelicula,
					        Id: this.identificador
				      	}
						axios.put('http://10.0.2.2:62270/api/Entradas/' + data.Id, data)
						.then(response => {
							EventBus.$emit("updateEntrada", this.data);
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
			    	axios.delete('http://10.0.2.2:62270/api/Entradas/' + this.identificador)
			        .then(response=> {
			          EventBus.$emit("updateEntrada", response.data);
			        })
					this.nuevo();
				}else{
		    		alert('Debes seleccionar una pelicula para poder borrar.');
		    	}
			}
		},
		created() {
    		this.entrada = this.$parent.entrada;
    		this.nombrePelicula = this.entrada.Pelicula;
    		this.duracionPelicula= this.entrada.Duracion;
		    this.salaPelicula= this.entrada.Sala;
		    this.identificador=this.entrada.Id;
  		}
	}

	function isFormularioValido(nomPelicula,durPelicula,salPelicula){
		let mensajeVal='';
		if(nomPelicula == null || nomPelicula=='' || (nomPelicula!=null && nomPelicula.trim()=='')){
			return 'El nombre de la pelicula debe estar relleno.'
		}
		if(durPelicula==0 || durPelicula >=400){
			return 'La duracion de la pelicula debe ser mayor que 0 y menor que 400 minutos.'
		}
		let mensaje = esEntero(durPelicula)
		if(mensaje!= ''){
			return 'Campo Duracion: '+ mensaje;
		}
		if(salPelicula==0 || salPelicula>=100){
			return 'La sala debe ser mayor que 0 y menor que 100.'
		}
		 mensaje = esEntero(salPelicula)
		if(mensaje!= ''){
			return 'Campo Sala: '+ mensaje;
		}
		return '';
	}
	function esEntero(numero){
	    if (numero % 1 == 0) {
	        return '';
	    }
	    else{
	        return 'Debes introducir un numero entero.';
	    }
	}
</script>