<template>
 <div>
  <HelloWorld></HelloWorld>
      <div id="app" class="container grid-md">
    <div class="columns">
      <div class="column col-12">
        <button @click="getToken()">
          Conectar ao servidor do WhatsApp
        </button>
        <button class="btn" @click="closeSession()">
          Fechar sessão
        </button>
        <button @click="getQrCode()">
          gerar QRcode
        </button>
        <br>
        {{ teste }}
        <br>
        {{ qrcoderen }}<br>
        <img width="500" height="500" style="object-fit:contain;" v-bind:src="imagem">
      </div>
    </div>
  </div>
  </div>
</template>

<script>
import axios from 'axios';
import io from 'socket.io-client';
import HelloWorld from './components/HelloWorld.vue';


const server_socket = 'https://whatsapp-free01.wppserver.com'

const socket = io(`${server_socket}`, {
  transportOptions: {
    polling: {
      extraHeaders: {
        'Authorization': 'Bearer abc',
      },
    },
  },
});


const serverAPI = 'https://whatsapp-free01.wppserver.com/api'
const session = 'Samsung';
const secretkey = 'LIzBSyCmNVzwt2ErKgtnZgLZ';
var token = '';
export default {
    data() {
        return {
            teste: "Conectar",
            tokenwhats: "teste",
            imagem: "https://gifs.eco.br/wp-content/uploads/2021/08/imagens-e-gifs-de-loading-7.gif",
            qrcoderen: "gerar2"
        };
    },
    watch: {
        checkSession() {
        }
    },
    methods: {
        async getToken() {
            this.teste = "Conectando..";
            axios({
                method: "POST",
                url: serverAPI + "/" + session + "/" + secretkey + "/generate-token",
            })
                .then((WppToken) => {
                console.log(WppToken);
                this.startSession(WppToken.data.token);
                token = WppToken.data.token;
                this.qrcoderen = WppToken.data.token;
            })
                .catch(function (error) {
                console.log(error);
            });
        },
        async startSession(WppToken) {
            axios({
                method: "POST",
                url: serverAPI + "/" + session + "/start-session",
                responseType: "stream",
                headers: {
                    "Content-Type": "application/json",
                    "Authorization": "Bearer " + WppToken
                },
                data: {
                    "webhook": "https://webhook.site/7cc2944d-3967-4cea-988c-d57ea80bce5f"
                }
            })
                .then((conectado) => {
                this.teste = conectado.data.status + conectado.data.session;
            })
                .catch(function (error) {
                console.log(error);
            });
        },
        getQrCode() {
            alert("chamei");
            socket.on("qrCode", (qrCode) => {
                console.log(qrCode);
                this.imagem = qrCode.data;
            });
            socket.on("session-logged", (session) => {
                console.log(session);
                if (session?.status == true) {
                    alert("Sucesso no check");
                }
            });
            socket.on("received-message", (message) => {
                console.log(message);
                if (session == message?.response?.session) {
                    alert(message?.response?.body);
                }
            });
        },
        async closeSession() {
            axios({
                method: "POST",
                url: serverAPI + "/" + session + "/close-session",
                headers: {
                    "Authorization": "Bearer " + token
                },
            })
                .then((desconectado) => {
                console.log(desconectado);
                this.teste = "Desconectado";
            })
                .catch(function (error) {
                console.log(error);
            });
        },
    },
    components: { HelloWorld }
}
</script>