<script>
let id = 0;

export default {
  data() {
    return {
      isHideCompleted: false,
      newTodo: "",
      todos: [],
    };
  },
  methods: {
    syncLocalStorage() {
      const data = JSON.stringify(this.todos);
      localStorage.setItem("data", data);
    },
    submitTodo() {
      this.todos.push({
        id: new Date().getTime(),
        text: this.newTodo,
        completed: false,
      });
      this.newTodo = "";

      this.syncLocalStorage();
    },
    deleteTodo(todo) {
      this.todos = this.todos.filter((item) => item !== todo);

      this.syncLocalStorage();
    },
    toggleCompleted(todo) {
      this.todos = this.todos.map((item) => {
        if (item.id === todo.id) {
          return { ...item, completed: !item.completed };
        }

        return item;
      });

      this.syncLocalStorage();
    },
    toggleShowCompleted() {
      this.isHideCompleted = !this.isHideCompleted;
    },
  },
  computed: {
    filteredTodos() {
      if (this.isHideCompleted) {
        return this.todos.filter((todo) => !todo.completed);
      }

      return this.todos;
    },
  },
  mounted() {
    if (localStorage.getItem("data")) {
      try {
        this.todos = JSON.parse(localStorage.getItem("data"));
      } catch (e) {
        localStorage.removeItem("todos");
      }
    }
  },
};
</script>

<template>
  <main class="container min-h-screen py-12 mx-auto">
    <form class="flex flex-col" @submit.prevent="submitTodo()">
      <input
        v-model="newTodo"
        type="text"
        name="todo"
        id="todo"
        placeholder="Write your todo here..."
        class="px-4 py-2 mx-6 text-lg bg-slate-100 rounded-xl md:mx-96"
      />
      <button
        type="submit"
        class="px-4 py-2 mx-6 mt-4 font-semibold text-white bg-green-700 rounded-xl md:mx-96"
      >
        <i class="fa fa-plus" aria-hidden="true"></i>
        Add
      </button>
    </form>
    <div class="flex items-center justify-between mx-6 my-4 md:mx-96">
      <h2 class="text-lg font-semibold">Todo List</h2>
      <button
        @click="toggleShowCompleted"
        class="px-3 py-1 text-sm font-semibold bg-slate-100 rounded-xl"
      >
        <span v-if="isHideCompleted">Show ALL</span>
        <span v-else>Hide Completed</span>
      </button>
    </div>

    <div v-if="todos.length <= 0" class="text-center">No Todos saved yet</div>
    <ul v-else class="mx-6 md:mx-96">
      <li
        v-for="todo in filteredTodos"
        :key="todo.id"
        class="flex items-center justify-between px-4 py-2 mt-2 bg-slate-100 rounded-xl"
      >
        <p
          :class="[
            'text-lg',
            'font-semibold',
            todo.completed ? 'line-through' : '',
          ]"
        >
          {{ todo.text }}
        </p>
        <div>
          <button
            @click="toggleCompleted(todo)"
            class="px-4 py-2 mx-2 text-sm text-white bg-blue-700 rounded-xl"
          >
            <i class="fa fa-check" aria-hidden="true"></i>
          </button>
          <button
            @click="deleteTodo(todo)"
            class="px-4 py-2 text-sm text-white bg-red-700 rounded-xl"
          >
            <i class="fa fa-trash" aria-hidden="true"></i>
          </button>
        </div>
      </li>
    </ul>
  </main>
</template>
