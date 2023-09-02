<template>
<main class="app">
  <section class="greeting">
    <h1 class="title">
      What's upp, <input type="text" placeholder="Name here" v-model="name">
    </h1>
  </section>
  <section class="create-todo">
    <h2>CREATE A TODO</h2>
    <form @submit.prevent="addTodo">
      <h4>What's on your todo list?</h4>
      <input 
        type="text" 
        placeholder="e.g. 'do work'"
        v-model="inputContent"
      >
      <h4>Add details if needed</h4>
      <!-- <input 
        type="text"
        placeholder="e.g. '12pm at Pizza Hut'"
        v-model="inputDetails"
      > -->
      <textarea 
        rows="5" 
        placeholder="e.g. '12pm at Pizza Hut'" 
        v-model="inputDetails">
      </textarea>
      <h4>Pick a category</h4>
      <div class="options">
        <label>
          <input 
            type="radio"
            name="category"
            value="business"
            v-model="inputCategory"
          >
          <span class="bubble business"></span>
          <div>Business</div>
        </label>
        <label>
          <input 
            type="radio"
            name="category"
            value="personal"
            v-model="inputCategory"
          >
          <span class="bubble personal"></span>
          <div>Personal</div>
        </label>
      </div>
      <input type="submit" value="Add todo">
    </form>
  </section>
  <section class="todo-list">
    <h3>TODO LIST</h3>
    <div class="list">
      <div 
        v-for="todo in todoAsc" 
        :key="todo.createdAt"
        :class="`todo-item ${todo.done && 'done'}`"
      >
        <label>
          <input type="checkbox" v-model="todo.done">
          <span :class="`bubble ${todo.category}`"></span>
        </label>
        <div class="todo-content">
          <input class="todo-title" type="text" v-model="todo.content">
          <textarea v-model="todo.details" rows="3" v-show="todo.showDetails"></textarea>
          <!-- <input class="todo-details" type="text" v-model="todo.details" v-show="showDetails"> -->
        </div>
        <div class="actions">
          <button class="details" @click="todo.showDetails = !todo.showDetails">Details</button>
          <button class="delete" @click="removeTodo(todo)">Delete</button>
        </div>
      </div>
    </div>
  </section>
</main>
</template>

<script setup>
import { ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')

const inputContent = ref('')
const inputDetails = ref('')
const inputCategory = ref(null)

const todoAsc = computed(() => todos.value.slice(0).sort((a,b) =>{
	return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if( inputContent.value.trim() === '' || inputCategory.value === null) {
    return
  }
  todos.value.push({
    content: inputContent.value,
    details: inputDetails.value,
    category: inputCategory.value,
    showDetails: false,
    done: false,
    createdAt: new Date().getTime()
  })

  inputContent.value = ''
  inputDetails.value = ''
  inputCategory.value = null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(name, newVal => {
  localStorage.setItem('name', newVal)
})

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})

onMounted(() => {
  name.value = localStorage.getItem('name') ?? ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>
