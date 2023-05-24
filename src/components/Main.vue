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
  <main class="min-h-screen container mx-auto py-12">
    <form class="flex flex-col" @submit.prevent="submitTodo()">
      <input
        v-model="newTodo"
        type="text"
        name="todo"
        id="todo"
        placeholder="Write your todo here..."
        class="bg-slate-100 px-4 py-2 text-lg rounded-xl mx-96"
      />
      <button
        type="submit"
        class="px-4 py-2 text-white bg-green-700 rounded-xl mx-96 mt-4"
      >
        Add
      </button>
    </form>
    <div class="flex justify-between mx-96 items-center">
      <h2 class="mt-4 mb-2 text-lg font-semibold">Todo List</h2>
      <button
        @click="toggleShowCompleted"
        class="bg-slate-100 rounded-xl text-sm px-3 py-1"
      >
        {{ isHideCompleted ? "Show All" : "Hide Completed" }}
      </button>
    </div>

    <div v-if="todos.length <= 0" class="text-center">No Todos saved yet</div>
    <ul v-else class="mx-96">
      <li
        v-for="todo in filteredTodos"
        :key="todo.id"
        class="bg-slate-100 rounded-xl px-4 py-2 mt-2 flex justify-between items-center"
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
            class="bg-blue-700 rounded-xl px-4 py-2 text-white text-sm mx-2"
          >
            Mark as Complete
          </button>
          <button
            @click="deleteTodo(todo)"
            class="bg-red-700 rounded-xl px-4 py-2 text-white text-sm"
          >
            Delete
          </button>
        </div>
      </li>
    </ul>
  </main>
</template>
