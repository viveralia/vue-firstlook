<script lang="ts">
  interface Todo {
    userId: number;
    id: number;
    title: string;
    completed: boolean;
  }

  interface Data {
    todos: Todo[];
    isLoading: boolean;
    newTodo: string;
    error: string;
    pageSize: string;
  }

  export default {
    name: "App",
    data() {
      return {
        todos: [],
        isLoading: false,
        newTodo: "",
        error: "",
        pageSize: localStorage.getItem("preferredPageSize") || "5",
      } as Data
    },
    methods: {
      addTodo: function () {
        const newTodo: Todo = { userId: 1, id: this.todos.length + 1, title: this.newTodo, completed: false };
        this.todos = [newTodo, ...this.todos];
        this.newTodo = "";
      },
      getTodos: async function () {
        try {
          this.error = "";
          this.isLoading = true;
          const url = `https://jsonplaceholder.typicode.com/todos?_start=0&_limit=${this.pageSize}`;
          const res = await fetch(url);
          const data = await res.json();
          this.todos = data;
        } catch (error) {
          this.error = error.message;
        } finally {
          this.isLoading = false;
        }
      },
    },
    watch: {
      pageSize: function (newPageSize) {
        this.getTodos();
        localStorage.setItem("preferredPageSize", newPageSize);
      }
    },
    mounted() {
      this.getTodos();
    }
  }
</script>

<template>
  <h1>Todos</h1>

  <select v-model="pageSize">
    <option value="5">5</option>
    <option value="10">10</option>
    <option value="30">30</option>
  </select>

  <form @submit.prevent="addTodo">
    <input type="text" v-model="newTodo" placeholder="New todo">
    <button type="text">Add todo</button>
  </form>

  <p v-if="isLoading">Loading todos...</p>
  <p v-if="error">{{ error }}</p>
  <ul v-if="!isLoading">
    <li v-for="todo in todos" v-bind:key="todo.id">
      <input type="checkbox" v-model="todo.completed" v-bind:id="todo.id">
      <label v-bind:for="todo.id">{{ todo.title }}</label>
    </li>
  </ul>

  <!-- <pre>{{ JSON.stringify(todos, null, 2) }}</pre> -->
</template>

<style scoped>
  ul {
    margin: 0;
    padding: 0;
  }

  li {
    list-style: none;
    margin-bottom: 1rem;
  }

  form {
    margin-bottom: 2rem;
  }
  
  select {
    margin-bottom: 1rem;
  }
</style>