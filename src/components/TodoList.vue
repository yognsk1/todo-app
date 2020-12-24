<template>
  <div id="todo-app">
    <form v-on:submit.prevent="addNewTodo">
      <label for="new-todo">Add a todo</label>
      <input
        v-model="newTodoText"
        id="new-todo"
        placeholder="E.g. Feed the cat"
      />
      <button>Add</button>
    </form>
    <ol v-if="todos.length">
      <li v-for="(todo, index) in todos" v-bind:key="todo.id">
        {{ todo.title }}
        <button v-on:click="removeTodo(index)" class="remove-btn">
          Remove
        </button>
      </li>
    </ol>
    <span v-else>No work today</span>
  </div>
</template>

<script>
export default {
  name: "TodoList",
  data: function() {
    return {
      newTodoText: "",
      todos: [
        {
          id: 0,
          title: "Do the dishes",
        },
      ],
    };
  },
  methods: {
    addNewTodo: function() {
      this.todos.push({
        id: this.todos.length,
        title: this.newTodoText,
      });
      this.newTodoText = "";
    },
    removeTodo: function(index) {
      this.todos.splice(index, 1);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#todo-app {
  max-width: 500px;
  margin: auto;
  min-height: 300px;
  border: solid 1px #ccc;
  border-radius: 4px;
  padding: 10px;
}
form {
  text-align: center;
  border-bottom: solid 1px #ccc;
  padding-bottom: 10px;
}
button {
  border-radius: 30px;
  border: solid 1px #000;
  padding: 5px;
  min-width: 62px;
  cursor: pointer;
  margin-left: 10px;
}
input {
  border: solid 1px #000;
  padding: 5px;
  border-radius: 5px;
  margin-left: 10px;
}
ol {
  text-align: left;
}
li {
  margin: 5px 0px;
}
.remove-btn:hover {
  background: red;
  color: #fff;
}
</style>
