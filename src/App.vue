<template>
  <div class="container">
      <div class="text-wrapper">
        <div id="setup" class="cascading-text--fold" :class="{animate : setupAnimating, animated: setupAnimated}">
          <span class="cascading-text__letter" v-for="(letter, index) in setupText" :style="delay(index)" :key="index">
            <div style="width: 15px" v-if="letter === ' '"></div>{{letter}}
          </span>
        </div>
        <div id="punchline" class="cascading-text--fold" :class="{animate : punchlineAnimating, animated: punchlineAnimated}">
          <span class="cascading-text__letter" v-for="(letter, index) in punchlineText" :style="delay(index)" :key="index">
            <div style="width: 15px" v-if="letter === ' '"></div>{{letter}}
          </span>
        </div>
      </div>
      <span id="btn" @click="tellJoke" :class="{'show': buttonShow}">Tell me a joke</span>
  </div>
</template>

<script>
  import axios from "axios";

  export default {
    name: 'App',
    data () {
      return {
        SYNTHESIS: window.speechSynthesis,
        setupText: [],
        setupAnimating: false,
        setupAnimated: false,
        punchlineText: [],
        punchlineAnimating: false,
        punchlineAnimated: false,
        buttonShow: false
      }
    },
    mounted(){
      if ('speechSynthesis' in window) {
        // LANGUAGE OF SPEECH IS THE LANG ATTRIBUTE ON THE HTML
        this.buttonShow = true;
      }else{
        alert("Sorry, your browser doesn't support text to speech!");
      }
    },
    methods:{
      delay(index) { /* Show each letter one by one */
        return {
          "-webkit-animation-delay": `${0.05 * index}s`,
          "animation-delay": `${0.05 * index}s`
        }
      },
      tellJoke: function(){
        var data = this; // Save this in new variable to make it accessable in the onstart and onend functions
        
        data.buttonShow = false;

        data.setupText = [];
        data.punchlineText = [];

        data.setupAnimating = false;
        data.punchlineAnimating = false;
        data.setupAnimated = false;
        data.punchlineAnimated = false;

        axios.get("https://official-joke-api.appspot.com/jokes/random")
        .then((response => {
            data.setupText = response.data.setup.split(""); // For letter animation
            data.punchlineText = response.data.punchline.split(""); // For letter animation

            const quoteUtterance = new SpeechSynthesisUtterance(response.data.setup);
            const personUtterance = new SpeechSynthesisUtterance(response.data.punchline);
            quoteUtterance.rate = personUtterance.rate = 0.75;
            data.SYNTHESIS.speak(quoteUtterance);
            data.SYNTHESIS.speak(personUtterance);
            
            quoteUtterance.onstart = function(){
              data.setupAnimating = true;
            }
            personUtterance.onstart = function(){
              data.setupAnimating = false;
              data.punchlineAnimating = true;
              data.setupAnimated = true;
            }
            personUtterance.onend = function(){
              data.buttonShow = true;
              data.punchlineAnimating = false;
              data.punchlineAnimated = true;
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
    -webkit-animation: color-change-5x 20s linear infinite alternate both;
	  animation: color-change-5x 20s linear infinite alternate both;
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

  .cascading-text__letter{
    transform-origin: bottom;
    transform: rotateX(90deg);
    width: fit-content;
    display: inline-block;
    transform-style: preserve-3d;
    -webkit-animation-duration: 0.5s;
    animation-duration: 0.5s;
    -webkit-animation-fill-mode: forwards;
    animation-fill-mode: forwards;
  }
  .animate .cascading-text__letter{
    -webkit-animation-name: fold;
    animation-name: fold;
  }
  .animated .cascading-text__letter{
    transform: rotateX(0);
  }

  #btn{
    background: white;
    border: 2px solid white;
    padding: 8px 15px;
    color: black;
    font-size: 15px;
    display: none;
    width: fit-content;
    margin: 0 auto;
    font-weight: 500;
    margin-top: 50px;
    cursor: pointer;
  }
  #btn:hover{
    background: transparent;
    color: white;
  }

  #btn.show{
    display: block;
  }

  @-webkit-keyframes fold{
    100% {
      transform: rotateX(0);
    }
  }
  @keyframes fold{
    100% {
      transform: rotateX(0);
    }
  }
  
  @-webkit-keyframes color-change-5x {
    0% {
      background: #355070;
    }
    25% {
      background: #6d597a;
    }
    50% {
      background: #b56576;
    }
    75% {
      background: #e56b6f;
    }
    100% {
      background: #eaac8b;
    }
  }
  @keyframes color-change-5x {
    0% {
      background: #355070;
    }
    25% {
      background: #6d597a;
    }
    50% {
      background: #b56576;
    }
    75% {
      background: #e56b6f;
    }
    100% {
      background: #eaac8b;
    }
  }

</style>