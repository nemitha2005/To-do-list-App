<script setup>
import { ref, onMounted, computed, watch } from 'vue'

// Refs for input and todos
const todos = ref([])
const newTodo = ref('')
const name = ref(localStorage.getItem('name') || '')

// Refs for form inputs
const input_content = ref('')
const input_category = ref(null)

// Computed property to sort todos by creation date
const todos_asc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt))

// Watcher to persist name in localStorage
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

// Method to add a new to-do
const addTodo = () => {
  if (input_content.value && input_category.value) {
    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      createdAt: Date.now()
    })
    // Clear input fields after adding
    input_content.value = ''
    input_category.value = null
  }
}

</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up <input type="text" placeholder="Name here" v-model="name"/>
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TO-DO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your To-Do list?</h4>
        <input 
          type="text" 
          placeholder="Enter a new To-Do" 
          v-model="input_content"/>
        
        <h4>Pick a category</h4>

        <div class="options">
          <label>
              <input 
                type="radio" 
                name="category"
                value="educational"
                v-model="input_category"/>
              <span class="bubble educational"></span>
              <div>Educational</div>
          </label>
          <label>
              <input 
                type="radio" 
                name="category"
                value="personal"
                v-model="input_category"/>
              <span class="bubble personal"></span>
              <div>Personal</div>
          </label>
        </div>
        <button type="submit">Add To-Do</button>
      </form>
    </section>
  </main>
</template>
