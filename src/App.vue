<template>
  <div class="container">
      <span id="setup">{{setup}}</span>
      <span id="punchline">&nbsp;{{punchline}}</span>
      <span id="btn" @click="tellJoke" :class="{'show': buttonShow}">{{buttonText}}</span>
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
        buttonText: "Tell me a joke",
        buttonShow: false
      }
    },
    mounted(){
      if ('speechSynthesis' in window) {
        // LANGUAGE OF SPEECH IS THE LANG ATTRIBUTE ON THE HTML
        console.log("Speech accepted");

        this.buttonShow = true;
      }else{
        console.log("Sorry, your browser doesn't support text to speech!");
      }
    },
    methods:{
      tellJoke: function(){

        var data = this;

        data.setup = "";
        data.punchline = "";

        axios.get("https://official-joke-api.appspot.com/jokes/random")
        .then((response => {
            data.buttonShow = false;

            data.setup = response.data.setup;
            data.punchline = response.data.punchline;
            data.quoteUtterance = new SpeechSynthesisUtterance(data.setup);
            data.personUtterance = new SpeechSynthesisUtterance(data.punchline);
            data.quoteUtterance.voice = data.voice.voice;
            data.personUtterance.voice = data.voice.voice;
            data.quoteUtterance.rate = data.personUtterance.rate = 0.75;
            data.SYNTHESIS.speak(data.quoteUtterance);
            data.SYNTHESIS.speak(data.personUtterance);
            data.quoteUtterance.onstart = function(){
            }
            data.personUtterance.onstart = function(){
            }
            data.personUtterance.onend = function(){
              data.buttonShow = true;
            }

          }
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
    font-style: italic;
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
    display: none;
    width: fit-content;
    margin: 0 auto;
    font-weight: 500;
    margin-top: 50px;
  }

  #btn.show{
    display: block;
  }
</style>
