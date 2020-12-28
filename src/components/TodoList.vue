<template>
  <div id="todo-app">
    <form v-on:submit.prevent="addNewTodo">
      <label for="new-todo">Add a todo</label>
      <input
        v-model="newTodoText"
        id="new-todo"
        placeholder="E.g. Feed the cat"
      />
      <button class="add-btn">Add</button>
    </form>
    <ol v-if="todos.length">
      <li
        v-for="todo in todos"
        v-bind:key="todo.id"
        v-bind:class="[todo.deleted && 'deleted-cls']"
      >
        {{ todo.title }}
        <button
          v-on:click="removeTodo(todo.id)"
          v-if="!todo.deleted"
          class="remove-btn"
        >
          Remove
        </button>
      </li>
    </ol>
    <div v-else style="margin:1em;">No task today..</div>
  </div>
</template>

<script>
import Vue from "vue";
import Component from "vue-class-component";

@Component
class TodoList extends Vue {
  newTodoText = "";
  todos = [];

  //lifecycle method
  beforeUpdate() {
    document.title =
      document.title.split(" ")[0] + " : " + this.todos.length + " task(s)";
  }
  addNewTodo() {
    if (this.newTodoText) {
      this.todos.push({
        id: this.todos.length,
        title: this.newTodoText,
      });
      this.newTodoText = "";
    }
  }
  removeTodo(id) {
    this.todos = this.todos.map(function(todo) {
      if (todo.id == id) {
        todo["deleted"] = true;
      }
      return todo;
    });
  }
}
export default TodoList;
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
  background: #fff;
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
.add-btn:hover {
  background: rgb(123, 68, 224);
  color: #fff;
}
.deleted-cls {
  text-decoration: line-through;
  color: red;
}
</style>
