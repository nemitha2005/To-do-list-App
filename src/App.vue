<script setup>
import { ref, onMounted, computed, watch } from "vue";

// Refs for input and todos
const todos = ref([]);
const newTodo = ref("");
const name = ref(localStorage.getItem("name") || "");

// Refs for form inputs
const input_content = ref("");
const input_category = ref(null);

// Computed property to sort todos by creation date
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

// Add a new todo
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = "";
  input_category.value = null;
};
// Delete a todo
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

// Watcher to persist name in localStorage
watch(
  name,
  (newVal) => {
    localStorage.setItem("name", newVal);
  },
  { immediate: true }
);

// Watcher to persist todos in localStorage
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

onMounted(() => {
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TO-DO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your To-Do list?</h4>
        <input
          type="text"
          placeholder="Enter a new To-Do"
          v-model="input_content"
        />

        <h4>Pick a category</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="educational"
              v-model="input_category"
            />
            <span class="bubble educational"></span>
            <div>Educational</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TO-DO LIST</h3>
      <div class="list">
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
