<template>
	<div id="form">
		<div v-if="activo" class="detalle">
			<h2>Detalle Entrada</h2>
			<div>
				<label>Pelicula:</label>
				<input type="text" class="form-control" id="peli" v-model="nombrePelicula" maxlength="40"/>
			</div>
			<div>
				<label>Duración:</label>
				<input type="number" class="form-control" id="duracion" name="duracion" v-model="duracionPelicula"/>
			</div>
			<div>
				<label>Sala:</label>
				<!--<input type="number" class="form-control" id="sala" name="sala" v-model="salaPelicula" /> -->
				<select class="form-control" v-model="salaPelicula">
					<option disabled value="">Selecciona número de sala</option>
					<option>1</option>
					<option>2</option>
					<option>3</option>
					<option>4</option>
				</select>
				<input type="radio" id="one" value="Normal" v-model="butacaPelicula">
				<label for="Normal">Normal</label>
				<br>
				<input type="radio" id="two" value="VIP" v-model="butacaPelicula">
				<label for="VIP">VIP</label>
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
		    	entrada:{},
		     	activo: true,
		     	nombrePelicula:undefined,
		     	duracionPelicula:undefined,
		     	salaPelicula:undefined,
		     	butacaPelicula:undefined,
		     	identificador:undefined
		    }
		},
		methods:{
		  	nuevo: function(){
		  		this.entrada = {};
		  		this.nombrePelicula=undefined;
		     	this.duracionPelicula=undefined;
		     	this.salaPelicula=undefined;
		     	this.butacaPelicula=undefined;
		     	this.identificador=-1;
		  	},

		  	enviar: function(){
		    	if(this.identificador <0){
		    		let mensajeValidacion = isFormularioValido(this.nombrePelicula,this.duracionPelicula,this.salaPelicula, this.butacaPelicula);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Pelicula: this.nombrePelicula,
					        Duracion: this.duracionPelicula,
					        Sala: this.salaPelicula,
					        Butaca: this.butacaPelicula,
					        Id: this.identificador
				      	}
				        axios.post('http://10.0.2.2:62270/api/Entradas/' ,data)
				        .then(response => {
				        	this.identificador = response.data.Id;
				          	EventBus.$emit("updateEntrada", this.data);
				          	swal(
								  '',
								  'Entrada creada!',
								  'success'
							)
				        })
				        this.nuevo();
			        }else{
			        	swal('', mensajeValidacion, 'error');
			        }
		    	}else{
		    		swal('', 'Debes vaciar y volver a rellenar el formulario para insertar.','');
		    	}
			},

			actualizar: function(){
		    	if(this.identificador >0){
		    		let mensajeValidacion = isFormularioValido(this.nombrePelicula,this.duracionPelicula,this.salaPelicula, this.butacaPelicula);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Pelicula: this.nombrePelicula,
					        Duracion: this.duracionPelicula,
					        Sala: this.salaPelicula,
					        Butaca: this.butacaPelicula,
					        Id: this.identificador
				      	}
						axios.put('http://10.0.2.2:62270/api/Entradas/' + data.Id, data)
						.then(response => {
							EventBus.$emit("updateEntrada", this.data);
							swal(
								  '',
								  'Entrada actualizada!',
								  'success'
								)
						})
						this.nuevo();
					}else{
						swal('', mensajeValidacion, 'error');
			        }
				}else{
		    		swal('', 'Debes seleccionar una pelicula para poder actualizar.', '');
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
		    		swal('', 'Debes seleccionar una pelicula para poder borrar.','');
		    	}
			}
		},
		created() {
    		this.entrada = this.$parent.entrada;
    		this.nombrePelicula = this.entrada.Pelicula;
    		this.duracionPelicula= this.entrada.Duracion;
		    this.salaPelicula= this.entrada.Sala;
		    this.butacaPelicula= this.entrada.Butaca;
		    this.identificador=this.entrada.Id;
  		}
	}

	function isFormularioValido(nomPelicula,durPelicula,salPelicula, butPelicula){
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

		if(salPelicula == null || salPelicula==''){
			return 'Debes escoger un número de sala.'
		}

/*		if(salPelicula==0 || salPelicula>=100){
			return 'La sala debe ser mayor que 0 y menor que 100.'
		}
/*		 mensaje = esEntero(salPelicula)
		if(mensaje!= ''){
			return 'Campo Sala: '+ mensaje;
		}*/
		return '';
	}
	function esEntero(numero){
	    if (numero % 1 == 0 && numero > 0) {
	        return '';
	    }
	    else{
	        return 'Debes introducir un numero entero y positivo.';
	    }
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