<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
  <Weather></Weather>
  
</template>

<script>
import Weather from './components/Weather.vue'


export default {
  name: 'App',
  data(){
    return{
      
    }
  },
  components: {
    Weather
  },
  methods:{
    
  }

}
</script>

<style>


body{
  animation-name: bgmove;
  animation-duration: 5s;
  animation-iteration-count: infinite;
  animation-direction: alternate;
}

@keyframes bgmove {
  from {background-color: #0351C1;}
  to {background-color: #002D6D;}
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: skyblue;
}


</style>
