<template>
  <div id="app">
    <Graph/>
  </div>
</template>

<script>
import Graph from "./components/AppGraph.vue";

export default {
  name: "app",
  components: {
    Graph
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  padding:0 30px;
}
</style>
