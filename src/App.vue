<template>
  <FiveLetters numberOfAttempts="6" letters="5" />
</template>

<script>
import FiveLetters from './components/FiveLetters.vue'

export default {
  name: 'App',
  components: {
    FiveLetters
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
:root {
  --letterBoxSize: 48px;
  --letterSize: 36px;
  --letterSpacingSize: 58px;
}
</style>
