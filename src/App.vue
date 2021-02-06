<template>
  <div class="container">
      <div id="app">
         <h1 id="quote-setup" class="fade">{{setup}}</h1>
         <h1 id="quote-punchline" class="fade">{{punchline}}</h1>
         <button id="btn" class="fade" @click="tellJoke">{{buttonText}}</button>
      </div>
  </div>
</template>

<script>
  import axios from "axios";

  export default {
    name: 'App',
    data () {
      return {
        SYNTHESIS: window.speechSynthesis,
        quoteUtterance: '',
        personUtterance: '',
        setup: '',
        punchline: '',
        voice: '',
        buttonText: "Tell me a joke"
      }
    },
    mounted(){
      if ('speechSynthesis' in window) {
        // LANGUAGE OF SPEECH IS THE LANG ATTRIBUTE ON THE HTML
        console.log("Speech accepted");
      }else{
        console.log("Sorry, your browser doesn't support text to speech!");
      }
    },
    methods:{
       tellJoke: function(){
        //document.getElementsByClassName('fade').fadeOut(function(){
           this.setup = "";
           this.punchline = "";
        //});

         axios.get("https://official-joke-api.appspot.com/jokes/random")
         .then((response => (
            this.setup = response.data.setup,
            this.punchline = response.data.punchline,
            this.quoteUtterance = new SpeechSynthesisUtterance(this.setup),
            this.personUtterance = new SpeechSynthesisUtterance(this.punchline),
            this.quoteUtterance.voice = this.voice.voice,
            this.personUtterance.voice = this.voice.voice,
            this.quoteUtterance.rate = this.personUtterance.rate = 0.85,
            //quoteUtterance.pitch = personUtterance.pitch = 1.1,
            this.SYNTHESIS.speak(this.quoteUtterance),
            this.SYNTHESIS.speak(this.personUtterance),
            this.buttonText = "Tell me another joke",
            this.quoteUtterance.onstart = function(){
              //document.getElementById('quote-setup').fadeIn(750);
            },
            this.personUtterance.onstart = function(){
              //document.getElementById('quote-punchline').fadeIn(750);
            },
            this.personUtterance.onend = function(){
              //document.getElementById('btn').fadeIn(1000);
            }
           )
         ));
       }
     }
  }
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
