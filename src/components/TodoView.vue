<script setup lang="ts">
import { ref } from 'vue';

interface Todo {
  id?: number;
  task: string;
  done: boolean;
}

const API_URL = "https://vaadin-todo-api.herokuapp.com/todos";

const task = ref("");
const error = ref("");
const todos = ref([] as Todo[]);

const fetchTodos = async () => {
  const todosResponse = await fetch(API_URL);
  todos.value = await todosResponse.json();
}

fetchTodos();

const addTodo = async () => {
  error.value = "";
  if (!task.value) {
    error.value ="Task cannot be empty";
    return;
  }
  const res = await fetch(API_URL, { method: "POST", body: task.value });
  todos.value.push(await res.json());
  task.value = "";
}

const deleteTodo = async (id: number) => {
  await fetch(`${API_URL}/${id}`, { method: "DELETE" });
  todos.value = todos.value.filter((t) => t.id !== id);
}
</script>

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

<style scoped>
.errors {
  color: red;
}
</style>
