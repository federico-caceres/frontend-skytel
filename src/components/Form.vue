<template>
    <div>
        <div class="row justify-content-center mt-5">
            <div class="col-md-6 col-sm-12 mx-1 mt-5">
                <div class="card mt-5">

                    <div class="card-body">
                        
                        <form novalidate="true">

                            <div class="row justify-content-center">
                                <h3>Registración</h3>
                            </div>
                            <div class="row justify-content-center">
                                
                                <div class="row mb-2">
                                    <div class="col-md-6 col-sm-12 mt-2">
                                        <input name="nombre" class="form-control" type="text" placeholder="Nombre" maxlength="25" v-model="form.nombre" >
                                    </div>
                                    <div class="col-md-6 col-sm-12 mt-2">
                                        <input name="apellido" class="form-control" type="text" placeholder="Apellido" maxlength="25" v-model="form.apellido">
                                    </div>
                                    <div class="col-md-6 col-sm-12 mt-2">
                                        <input name="tel" class="form-control" type="number" placeholder="Teléfono" minlength="5" maxlength="10" v-model="form.tel" required>
                                    </div>
                                    <div class="col-md-6 col-sm-12 mt-2">
                                        <input name="email" class="form-control" type="email" placeholder="Email" v-model="form.email" required>
                                    </div>
                                </div>
                            </div>

                            <p v-if="errors.length">
                                <ul>
                                    <div class="alert alert-danger" role="alert">
                                        <b>Por favor, corrija el(los) siguiente(s) error(es):</b>
                                        <li v-for="error in errors" :key="error">
                                            {{ error }}
                                        </li>
                                    </div>
                                </ul>
                            </p>

                            <div v-show="token" class="alert alert-success" role="alert">
                                <b> Registro exitoso! Token recibido: {{ token }}</b>
                            </div>

                            <div class="row mt-3 justify-content-center">
                                <div class="col-md-3 col-sm-6">
                                    <input class="btn btn-block btn btn-secondary" value="Enviar!" @click="validateForm">
                                </div>
                            </div>
                        
                        </form>

                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'

    export default {
        name: 'Form',
        components:{
            
        },    
        data(){
            return{
                errors:[],
                form:{
                    nombre:null,
                    apellido:null,
                    tel:null,
                    email:null
        
                },
                token:null
            }
        },
        methods: {
            postRegistrar(){
                const params = { nombre: this.form.nombre,
                                apellido: this.form.apellido,
                                tel: this.form.tel,
                                email: this.form.email };
                
                axios.post("http://localhost:8000/api/register", params)
                .then( response => {
                    console.log(response.data);

                    if (response.status == 200) {
                        if (response.data.error) {
                            this.errors.push('Error: '+response.data.error);
                        }else{
                            this.token = response.data.token;
                        }
                    }else{
                        this.errors.push('Error en respuesta del servidor: '+response.status);
                    }
                }).catch(e =>{
                    this.errors.push('El servidor no response, intente mas tarde! '+e);
                });
            },
            validateForm: function(e){
                this.errors = [];

                if ( !this.form.nombre) {
                    this.errors.push('Debes agregar el nombre!');
                }else if (this.form.nombre.length > 25) {
                    this.errors.push('El nombre no puede ser mayor a 25 caracteres!');
                }

                if ( !this.form.apellido) {
                    this.errors.push('Debes agregar el apellido!');
                }else if (this.form.nombre.length > 25) {
                    this.errors.push('El apellido no puede ser mayor a 25 caracteres!');
                }

                if( !this.form.tel){
                    this.errors.push('Debes agregar el número de teléfono!');
                }else if (this.form.tel.length < 5) {
                    this.errors.push('El número de teléfono no puede ser menor a 5 números!');
                }else if(this.form.tel.length > 10){
                    this.errors.push('El número de teléfono no puede ser mayor a 10 números!');
                }

                if( !this.form.email){
                    this.errors.push('Debes agregar el email!');
                }else if ( !this.validEmail(this.form.email) ) {
                    this.errors.push('El correo electrónico debe ser válido!');
                }

                if (!this.errors.length) {
                    this.postRegistrar();
                    return true;
                }

                e.preventDefault();

            },
            validEmail: function (email) {
                var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;// eslint-disable-line
                return re.test(email);
            }
        },
    }
</script>

<style>
    
</style>