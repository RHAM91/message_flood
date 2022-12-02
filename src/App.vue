<template>
    <div class="contenedor">
        <div class="cabecera">

        </div>
        <div class="cuerpo">
            <b-container fluid="">
                <b-row>
                    <b-col sm="12" class="mt-3">
                        <label for="">Token bot Telegram</label>
                        <b-form-input type="text" id="btoken" v-model="token_bot_telegram" size="sm"></b-form-input>
                    </b-col>
                    <b-col sm="5" class="mt-3">
                        <label for="">Chat ID</label>
                        <b-form-input type="text" v-model="id_chat" size="sm"></b-form-input>
                    </b-col>
                    <b-col sm="3" class="mt-3">
                        <label for="">Invervalos (seg)</label>
                        <b-form-input type="number" v-model="segundos" size="sm"></b-form-input>
                    </b-col>
                    <b-col sm="4" class="mt-3">
                        <label for="">Número de mensajes</label>
                        <b-form-input type="number" v-model="paquetes" size="sm"></b-form-input>
                    </b-col>
                    <b-col sm="12" class="mt-3">
                        <label for="">Mensaje</label>
                        <b-form-textarea
                            v-model="mensaje"
                            placeholder="Enter something..."
                            rows="3"
                            max-rows="6"
                        ></b-form-textarea>
                    </b-col>
                    <!-- <b-col sm="6" class="mt-3">
                        <b-button type="button" variant="success" size="sm" block="" @click="arranque">Ejecutar</b-button>
                    </b-col>
                    <b-col sm="6" class="mt-3">
                        <b-button type="button" variant="danger" size="sm" block="" @click="detener(1)">Detener</b-button>
                    </b-col> -->

                    <b-col sm="12" class="mt-3">
                        <div class="cmd" id="cmd">
                            
                        </div>
                    </b-col>
                </b-row>

                <div v-if="enlinea == true" class="btn_inicio" @click="arranque">
                    <i class="fas fa-play"></i>
                </div>

                <div v-if="enlinea == false" class="btn_detener" @click="detener(1)">
                    <i class="fas fa-stop"></i>
                </div>


            </b-container>
        </div>
    </div>
</template>

<script>

import axios from 'axios'
import { minix } from './components/functions/alertas'

var intervalo

export default {
    name: 'Formulario',
    data() {
        return {
            token_bot_telegram: '',
            id_chat: '',
            paquetes: 50,
            segundos: 30,
            mensaje: 'PWNED!!!!!!! BY RIOKO!',
            contador: 1,
            enlinea: true
        }
    },
    methods: {

        arranque(){

            if (this.token_bot_telegram == '' || this.id_chat == '' || this.paquetes == '' || this.segundos == '' || this.mensaje == '') {
                minix({icon: 'error', mensaje: 'TODOS los campos debene estar llenos', tiempo: 3000})    

            }else{

                minix({icon: 'success', mensaje: 'Iniciando ataque', tiempo: 3000})
                this.enlinea = false
    
                intervalo = setInterval(() => {
                    this.enviar()
                }, 3000);
            }

        },
        detener(param){
            clearInterval(intervalo)
            intervalo = null
            this.contador = 1

            if (param == 1) {
                document.getElementById('cmd').innerHTML = ''
            }

            minix({icon: 'info', mensaje: 'Se ha detenido el ataque', tiempo: 3000})
            this.enlinea = true
        },
        async enviar(){

            if (this.contador >= this.paquetes) {
                clearInterval(intervalo)
                intervalo = null
                this.contador = 1

                document.getElementById('cmd').innerHTML = `DESCANSANDO ${this.segundos} SEGUNDOS......`

                setTimeout(() => {
                    this.arranque()
                }, this.segundos * 1000);
            }else{
                try {
    
                    let formulario = {
                        chat_id: this.id_chat,
                        text: this.mensaje
                    }
    
                    let f = await axios.post(`https://api.telegram.org/bot${this.token_bot_telegram}/sendMessage`, formulario)
                    let d = `<div> <b>#Mensaje</b>: ${this.contador} <br><br><b>OK</b>: ${f.data.ok} <br> <b>Message ID</b>: ${f.data.result.message_id} <br> <b>Chat</b>: ${JSON.stringify(f.data.result.chat)} </div>`
                    document.getElementById('cmd').innerHTML = d
                    this.contador += 1
                
                    
                    
                } catch (e) {
                    let d = `<div><b>OK</b>: ${e.response.data.ok} <br> <b>Error Code</b>: ${e.response.data.error_code} <br> <b>Description</b>: ${e.response.data.description}</div>`
                    document.getElementById('cmd').innerHTML = d

                    this.detener(2)
                }
            }

        }
    },
    mounted() {
        document.getElementById('btoken').focus()
    },
}
</script>

<style>
    .contenedor{
        width: 100%;
        height: 100vh;
        
    }

        .cabecera{
            width: 100%;
            height: 40px;
            background-color: #225560;
        }

        .cuerpo{
            width: 100%;
            height: calc(100% - 40px);
            background-color: rgba(204, 204, 204, 0.067)
        }
            .cmd{
                width: 100%;
                height: 150px;
                background-color: #eaf5f4;
                padding: 10px;
                font-size: 12px;
            }

            .btn_inicio{
                position: fixed;
                bottom: 15px;
                right: 15px;
                width: 50px;
                height: 50px;
                border-radius: 50%;
                color: white;
                cursor: pointer;
                user-select: none;
                display: flex;
                justify-content: center;
                align-items: center;
                background-color: #00afb9;
            }

            .btn_detener{
                position: fixed;
                bottom: 15px;
                right: 15px;
                width: 50px;
                height: 50px;
                border-radius: 50%;
                color: white;
                cursor: pointer;
                user-select: none;
                display: flex;
                justify-content: center;
                align-items: center;
                background-color: #f07167;
            }
</style>