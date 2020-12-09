<template>
  <!-- 
    Programa hecho por Luis Alejandro Pérez Sarmiento
    con fines educativos para la materia algoritmos 
   -->
  <v-container style="padding-left: 0px" class="blue-grey darken-4">
    <!-- {{deck[0]}} -->
     <v-app-bar
      color="deep-purple accent-4"
      dense
      dark
    >
      <v-app-bar-nav-icon></v-app-bar-nav-icon>

      <v-toolbar-title>Fantasma blitz</v-toolbar-title>

      <v-spacer></v-spacer>

    
      Cantidad de figuras 
      <v-menu
        left
        bottom
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            icon
            v-bind="attrs"
            v-on="on"
          >
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </template>

        <v-list  v-for="n in numeros"
            :key="n" >
          <v-list-item
            @click="CantidadObjetos(n)"
          >
            <v-list-item-title  > {{ n }}</v-list-item-title>
          
          </v-list-item>
        </v-list>
      </v-menu>

      
    </v-app-bar>





    <v-dialog
      v-model="dialog"
      max-width="290" 
    >
      <v-card  :color="mensaje"   >
        <v-card-title  v-if="correcto" class="headline">Respuesta correcta</v-card-title>

        <v-card-text v-if="correcto">
          Respuesta correcta
        </v-card-text>

        <v-card-title  v-if="!correcto" class="headline">Respuesta incorrecta</v-card-title>

        <v-card-text v-if="!correcto">
          Respuesta incorrecta
        </v-card-text>


        <v-card-actions>
          <v-spacer></v-spacer>

          <v-btn 
           v-if="!correcto"
            color="error"
            @click="dialog = false"
          >
            Intenta de nuevo
          </v-btn>

          <v-btn
           v-if="correcto"

            color="success"
            @click="dialog = false"
          >
            Muy bien
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>



    <v-row justify="space-around">
      <v-col cols="12" md="4">
    <v-card
    max-width="250"
    tile
    elevation="24"
    min-height="900"
    >
    <v-list dense>
      <v-subheader>OBJETOS</v-subheader>
      <v-list-item-group
        v-model="selectedItem"
        color="primary"
        style="max-height: 800px; "   class="overflow-y-auto"
      >
        <v-list-item
          v-for="(objeto, i) in cantidadObjetos"
          :key="i"
        >
          <v-card height="200" width="200"
              v-on:click='checkAnswer(i+1)'  >
              <v-img
              v-on:click='checkAnswer(i+1)'
               :src="getImgUrl(i+1, i+1)"
                max-height="100%"
                contain
                class="grey darken-4"
              >
              </v-img>
            </v-card>
      
        
        </v-list-item>
      </v-list-item-group>
    </v-list>
    </v-card>
    </v-col>



    <v-col justify=center aligment=center >


       
        <v-btn
          :loading="loading"
          :disabled="loading"
          color="secondary"
          @click="loader = 'loading'"
          v-on:click="beforeCard()"
        >
          Carta anterior
        </v-btn>
         <v-btn
          :loading="loading"
          :disabled="loading"
          color="secondary"
          @click="loader = 'loading'"
          v-on:click="createNewCrad()"
        >
          Carta aleatoria
        </v-btn>
        <v-btn
          :loading="loading"
          :disabled="loading"
          color="secondary"
          @click="loader = 'loading'"
          v-on:click="nextCard()"
        >
          Siguiente carta
        </v-btn>
        

      <v-container
        fluid
        grid-list-lg
        fill-height
        style="padding-right: 0px" height = "700">
        
        <!-- overflow-y-auto -->
        <v-sheet class="overflow-y"  color="amber accent-4" elevation="24"  style="max-height: 700;"   >
          <v-row  >
              <v-col
                 v-for="(i) in objetosCarta" :key="i"
                class="d-flex child-flex"
                cols="0"
                
              >
                <v-img
                  
                height="250"
                 width="250"
                 
                  :src="getImgUrl(carta.objetos[i-1], carta.colores[i-1])"
                  :lazy-src="getImgUrl(carta.objetos[i-1], carta.colores[i-1])"
                  contain
                  class="yellow lighten-2"
                >
               
                  <template v-slot:placeholder>
                    <v-row
                      class="fill-height ma-0"
                      align="center"
                      justify="center"
                    >
                      <v-progress-circular
                        indeterminate
                        color="grey lighten-5"
                      ></v-progress-circular>
                    </v-row>
                  </template>
                </v-img>
                
              </v-col>
            </v-row>

        </v-sheet>
      </v-container>
    </v-col>
    </v-row>
  </v-container>
</template>

<script lang="js">
  // import Vue from 'vue'
  import { Component, Vue } from 'vue-property-decorator';
  import file3 from '@/assets/txt/cartas3.txt'
  import file5 from '@/assets/txt/cartas5.txt'
  import file7 from '@/assets/txt/cartas7.txt'
  import file9 from '@/assets/txt/cartas9.txt'

  export default {
    data(){
        return{
          correcto:false,
          mensaje:"error",
          dialog:false,
          selectedItem: null,
          loader: null,
          deck:[],
          loading: false,
          objetosCarta:2,
          cantidadObjetos:5,
            model:0,
            carta:null,
          numeros:[3,5,7,9],
          indice:0
        }
    },
    methods: {
      getCards(file){
        this.$data.deck = []
        this.$data.objetosCarta = 0
        this.$data.cantidadObjetos = 0
        this.$data.indice = 0



        let temp="";
        const temporal = [];
        for (let index = 0; index < file.length; index++) {
            if (file[index] =='\n' ) {
              temporal.push(temp)
              temp="";
            }else if (file[index] !=" " ){
              temp = temp + file[index]
            }
        }
        let cartaNueva={
            objetos:[],
            colores:[],
            igualdad:false
          }
        for (let i = 0; i < temporal.length; i++) {
          
          cartaNueva={
            objetos:[],
            colores:[],
            igualdad:false
          }
          let k=0;
          for (let j = 0; j < temporal[i].length; j++) {
            if (temporal[i][j] == "T" ) {
                cartaNueva.igualdad=true
            }
            if (parseInt(temporal[i][j])) {
              
              if (k%2==0) 
                  cartaNueva.objetos.push(parseInt(temporal[i][j]) )  
              else
                  cartaNueva.colores.push(parseInt(temporal[i][j]) )
              k=k+1
            }

          }
          this.$data.deck.push(cartaNueva)
        }
        this.$data.carta=this.$data.deck[0]
         this.$data.objetosCarta = this.$data.carta.objetos.length
         this.$data.cantidadObjetos = (this.$data.carta.objetos.length *2)  +1
      },
      getImgUrl(objeto, color) {
        return require('../assets/'+objeto+color+'.png')
      },
      createNewCrad(){
        
        // this.loading=true;
        let aleatorio = 0;
        aleatorio = Math.floor(Math.random() * (this.$data.deck.length - 0)) + 0;
        this.$data.indice = aleatorio
        this.$data.carta=this.$data.deck[aleatorio]
        // setTimeout(() => this.loading=false, 1000)
      },
      nextCard(){
        
        // this.loading=true;
        
        this.$data.indice = this.$data.indice + 1 
        if (this.$data.indice >= this.$data.deck.length ) {
           this.$data.indice = 0
        }
        this.$data.carta=this.$data.deck[this.$data.indice]
        // setTimeout(() => this.loading=false, 1000)
      },
      beforeCard(){
        this.$data.indice = this.$data.indice - 1
        if (this.$data.indice < 0 ) {
           this.$data.indice = this.$data.deck.length-1
        }

        this.$data.carta=this.$data.deck[this.$data.indice]
      },
      checkAnswer(respuesta){
        if (this.carta.igualdad) {
          let numero=0;
          numero=0
          for (let i = 0; i < this.carta.colores.length; i++) {
            if (this.carta.objetos.find( elemento =>elemento==this.carta.colores[i])) {
              numero = this.carta.colores[i];
              break
            }
          }
          if (numero==respuesta) {
            console.log("bien");

            this.dialog=true;
            this.correcto = true;
            this.mensaje = "success";
          }
          else{
            this.dialog=true;
            this.correcto = false;
            this.mensaje = "error";

            console.log("mal");
          }


        }
        else{
          let array = []
          array = this.carta.colores.concat(this.carta.objetos)
          let condicion = true
          for (let i = 0; i < array.length; i++) {
            if (array[i]==respuesta ) {
              console.log("mal");
              condicion = false;
              this.dialog=true;
              this.correcto = false;
              this.mensaje = "error";
              break
            }            
          }
          if (condicion) {
            this.dialog=true;
            this.correcto = true;
            this.mensaje = "success";
            console.log("bien");
          }
        }

      },
      CantidadObjetos(i){


        switch (i) {
          case 3:
            this.getCards(file3)
            break;
          case 5:
            this.getCards(file5)
            break;
          case 7:
            this.getCards(file7)
            break;
          case 9:
            this.getCards(file9)
            break;
          default:
            break;
        }
      
      },

    },
    
    created(){

      this.getCards(file3)
    },
    
  }
</script>

<style>
  .custom-loader {
    animation: loader 1s infinite;
    display: flex;
  }
  @-moz-keyframes loader {
    from {
      transform: rotate(0);
    }
    to {
      transform: rotate(360deg);
    }
  }
  @-webkit-keyframes loader {
    from {
      transform: rotate(0);
    }
    to {
      transform: rotate(360deg);
    }
  }
  @-o-keyframes loader {
    from {
      transform: rotate(0);
    }
    to {
      transform: rotate(360deg);
    }
  }
  @keyframes loader {
    from {
      transform: rotate(0);
    }
    to {
      transform: rotate(360deg);
    }
  }
</style>