<template>
  <div id="app">
    <div class="container">
      <span id="setup">{{setup}}</span>
      <span id="punchline">&nbsp;{{punchline}}</span>
      <span id="btn" @click="tellJoke">{{buttonText}}</span>
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
  @import url('https://fonts.googleapis.com/css2?family=Kanit:wght@500;900&display=swap');

  body{
    background: black;
  }

  #app {
    font-family: Kanit, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: white;
    margin-top: 60px;

    text-transform: uppercase;
    font-weight: 900;
    font-size: 2em;
  }

  .container{
    padding: 30px;
    position: absolute;
    top: 50%;
    left: 50%;
    width: fit-content;
    transform: translate(-50%, -50%);
    max-width: 70vw;
    letter-spacing: 5px;
  }

  #setup{
    -webkit-text-stroke: 2px white;
    text-stroke: 2px white;
    -webkit-text-fill-color: transparent;
    text-fill-color: transparent;
    color: transparent;
  }
  #punchline{
    color: white;
  }

  #btn{
    background: white;
    padding: 8px 15px;
    color: black;
    font-size: 15px;
    display: block;
    width: fit-content;
    margin: 0 auto;
    font-weight: 500;
    margin-top: 50px;
  }
</style>
