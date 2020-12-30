<template>
  <b-container fluid="sm">
    <TodoList />
  </b-container>
</template>

<script>
import Vue from "vue";
import Component from "vue-class-component";
import TodoList from "./components/TodoList.vue";

@Component({
  components: {
    TodoList,
  },
})
class App extends Vue {}
export default App;
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
</style>
