<template>
  <h1>Todo</h1>
  <form @submit.prevent="addTodo">
    <input type="text" v-model="task" />
    <button type="submit">Add</button>
  </form>
  <div className="errors">{{error}}</div>
  <ul>
    <li v-for="todo in todos" :key="todo.id">
      {{todo.task}}
      <button @click="deleteTodo(todo.id)">Delete</button>
    </li>
  </ul>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

interface Todo {
  id?: number;
  task: string;
  done: boolean;
}

const API_URL = "https://vaadin-todo-api.herokuapp.com/todos";
export default defineComponent({
  name: 'TodoView',

  data() {
    return {
      todos: [] as Todo[],
      task:"",
      error: ""
    }
  },

  methods: {
    async addTodo() {
      this.error = ""
      if (!this.task) {
        this.error ="Task cannot be empty";
        return;
      }
      const res = await fetch(API_URL, { method: "POST", body: this.task });
      this.todos.push(await res.json());
      this.task = ""
    },

    async deleteTodo(id: number){
      await fetch(`${API_URL}/${id}`, { method: "DELETE" });
      this.todos = this.todos.filter((t) => t.id !== id);
    },
  },

  async created() {
    const todoJson = await fetch(API_URL);
    this.todos = await todoJson.json();
  }
})
</script>