<template>
  <PageTemplate/>
</template>

<script>
import PageTemplate from './components/PageTemplate.vue'

export default {
  name: 'App',
  components: {
    PageTemplate
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
  height: 100%;
  box-sizing: border-box;
  overflow-x: hidden;
  overflow-y: hidden;
  min-height: 100%;
  width: 100vw;
  

}

body {
  padding:0;
  margin: 0;
  height: 100%;
  width: 100%;
}
</style>
