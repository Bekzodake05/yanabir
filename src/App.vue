<template>
  <weather/>
</template>

<script>
import weather from "./components/weather.vue"
export default {
  name: 'App',
  components: { weather},
  data(){
    return{

    }
  },

  methods: {
    
    
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #111;
  width: 100%;
  min-height: 100vh;
}
</style>
