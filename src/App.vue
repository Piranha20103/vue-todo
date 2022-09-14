<template>
  <div class="container" :class="{ dark: darkMode }">
    <h1 class="header">Vue 3 TODO App</h1>
    <input
      placeholder="Add TODO"
      class="input"
      :class="{ dark: darkMode }"
      v-model="input"
      @keyup="handleEnter"
    />
    <div class="button-container">
      <button
        class="dark-mode-button"
        :class="{ dark: darkMode }"
        @click="toggleDarkMode"
      >
        Toggle dark mode
      </button>
      <button class="clear-button" @click="clearTodo">All clear</button>
    </div>
    <ul class="list">
      <li class="subheader">All ({{ todosCount }})</li>
      <Todo
        v-for="todo in todos.active"
        :key="todo.id"
        :todo="todo"
        :remove-todo="() => removeTodo(todo.id, true)"
        :complete-todo="() => completeTodo(todo.id)"
      />
      <li v-if="todos.completed.length > 0" class="subheader">
        Completed ({{ todos.completed.length }})
      </li>
      <DoneTodo
        v-for="todo in todos.completed"
        :key="todo.id"
        :todo="todo"
        :remove-todo="() => removeTodo(todo.id, true)"
        :restore-todo="() => restoreTodo(todo.id)"
      />
    </ul>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent, provide, reactive, ref } from "vue";
import DoneTodo from "./components/DoneTodo.vue";
import Todo from "./components/Todo.vue";

interface TODO {
  id: string;
  value: string;
}
interface TODOS {
  active: TODO[];
  completed: TODO[];
}

const randomId = (): string => {
  return `_${Math.random().toString(36).slice(2, 11)}`;
};

export default defineComponent({
  components: { DoneTodo, Todo },
  setup() {
    const darkMode = ref(false);
    const todos = reactive<TODOS>({
      active: [],
      completed: [],
    });
    const todosCount = computed(
      () => todos.active.length + todos.completed.length
    );
    const input = ref("");
    const addTodo = () => {
      if (input.value) {
        todos.active.push({ id: randomId(), value: input.value });
        input.value = "";
      }
    };
    const removeTodo = (id: string, fromActive?: boolean) => {
      if (fromActive) {
        todos.active.splice(
          todos.active.findIndex((todo) => todo.id === id),
          1
        );
      } else {
        todos.completed.splice(
          todos.completed.findIndex((todo) => todo.id === id),
          1
        );
      }
    };
    const restoreTodo = (id: string) => {
      const todo = todos.completed.find((todo) => todo.id === id);

      if (todo) {
        todos.completed.splice(todos.completed.indexOf(todo), 1);
        todos.active.push(todo);
        console.log("hey!");
      }
    };
    const completeTodo = (id: string) => {
      const todo = todos.active.find((todo) => todo.id === id);

      if (todo) {
        todos.active.splice(todos.active.indexOf(todo), 1);
        todos.completed.push(todo);
      }
    };
    const handleEnter = (event: KeyboardEvent) => {
      if (event.key === "Enter") {
        addTodo();
      }
    };
    const clearTodo = () => {
      if (todosCount) {
        todos.active.splice(0, todos.active.length);
        todos.completed.splice(0, todos.completed.length);
      }
    };
    const toggleDarkMode = () => {
      darkMode.value = !darkMode.value;
    };

    provide("dark-mode", darkMode);

    return {
      darkMode,
      input,
      todos,
      todosCount,
      handleEnter,
      removeTodo,
      restoreTodo,
      completeTodo,
      toggleDarkMode,
      clearTodo,
    };
  },
});
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: Arial, sans-serif;
  color: #374151;
}
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin: auto;
  width: 30rem;
  max-width: 100%;
  background: #f3f4f6;
  border-radius: 1rem;
  padding: 1.5rem;
}
.container.dark {
  background: #1f2937;
  color: #fff;
}
.header {
  margin: 0;
}
.subheader {
  font-weight: bold;
  margin: 1rem 0;
}
.input {
  background: #e5e7eb;
  box-sizing: border-box;
  width: 100%;
  border-radius: 1rem;
  font-size: 1.25rem;
  padding: 0.5rem;
  outline: none;
  border: none;
  margin: 1rem 0;
}
.input.dark {
  background: #374151;
  color: #fff;
}
.list {
  font-size: 1.25rem;
  list-style: none;
  padding: 0;
  margin: 0;
  width: 100%;
}
.item {
  background: #e5e7eb;
  padding: 1rem;
  border-radius: 1rem;
  margin-top: 1rem;
}
.item.dark {
  background: #374151;
}
.item-buttons {
  display: flex;
  justify-content: flex-end;
}
.item-buttons > button {
  transition: transform 150ms ease-out;
  cursor: pointer;
  padding: 0.5rem;
  border-radius: 0.5rem;
  margin-left: 0.5rem;
  color: #fff;
  outline: none;
  border: none;
}
.item-buttons > button:hover {
  transform: scale(1.05);
}
.button-container {
  display: flex;
  justify-content: center;
  align-items: center;
}
.dark-mode-button,
.clear-button {
  transition: transform 150ms ease-out;
  cursor: pointer;
  padding: 0.5rem;
  border-radius: 1rem;
  margin-left: 0.5rem;
  background: #374151;
  color: #fff;
  outline: none;
  border: none;
}
.dark-mode-button:hover,
.clear-button:hover {
  transform: scale(1.05);
}
.dark-mode-button.dark {
  background: #f3f4f6;
  color: #374151;
}
.clear-button {
  background: #1dccd8;
}
.done-button {
  background: #10b981;
}
.remove-button {
  background: #ef4444;
}
.restore-button {
  background: #1863a1;
}
</style>
