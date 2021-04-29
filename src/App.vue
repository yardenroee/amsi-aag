<template>
  <Home />
</template>

<script>
import Home from "./components/Home.vue";
export default {
  name: "App",
  components: {
    Home,
  },
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Oxygen:wght@300;400;700&display=swap');
#app {
  font-family: Oxygen, Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #676767;
  margin-top: 60px;
}
</style>
