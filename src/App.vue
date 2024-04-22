<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import { TrashIcon, PencilIcon, CheckIcon, XMarkIcon} from '@heroicons/vue/24/solid'

let id = 0

const newTodo = ref('')
const todos = ref([])
const oldValue = ref('')
const filter = ref('')

const filteredTodos = computed(() => {
  if(filter.value === 'all') {
    return todos.value
  } else if (filter.value === 'active') {
    return todos.value.filter(todo => !todo.done)
  } else if (filter.value === 'completed') {
    return todos.value.filter(todo => todo.done)
  }
  return todos.value
})

onMounted(() => {
  const data = localStorage.getItem('todos')
  data ? todos.value = JSON.parse(data) : null
})

function addTodo() {
  todos.value.push({ id: id++, text: newTodo.value, done: false, editing: true })
  localStorage.setItem('todos', JSON.stringify(todos.value)) //первым  арг название  arr вторым арг что сохранить //
  newTodo.value = ''
}

function removeTodo(todo) {
  todos.value = todos.value.filter(t => t !== todo)
  localStorage.setItem('todos', JSON.stringify(todos.value))
}

function editTodo(index) {
    todos.value[index].editing = !todos.value[index].editing
    oldValue.value = todos.value[index].text
    localStorage.setItem('todos', JSON.stringify(todos.value))
}

function backTodo(index) {
  todos.value[index].editing = !todos.value[index].editing
   todos.value[index].text = oldValue.value
   localStorage.setItem('todos', JSON.stringify(todos.value))
}

function checkTodo(index) {
  todos.value[index].done = !todos.value[index].done
  localStorage.setItem('todos', JSON.stringify(todos.value))
}
 
</script>

<template>
  <div class="center w-80">
  <h1 class="text-3xl font-bold underline text-center">Todo List</h1>
  <div class="flex gap-4">
    <button @click="filter = 'all'" class="border-2 w-24">All</button>
    <button @click="filter = 'active'" class="border-2 w-24">Active</button>
    <button @click="filter = 'completed'" class="border-2 w-24">Completed</button>
  </div>
  <form @submit.prevent="addTodo">
    <input v-model="newTodo" required placeholder="new todo">
    <button>Add Todo</button>
  </form>
  <ul>
    <li v-for="(todo, index) in filteredTodos" :key="todo.id">
      <input @click="checkTodo(index)" type="checkbox" v-model="todo.done">
      <span v-if="todo.editing" :class="{ done: todo.done }">{{ todo.text }}</span>
      <input v-else v-model="todo.text" class="border-2" required>
      <button v-if="todo.editing" @click="removeTodo(todo)">
        <TrashIcon class="h-4 w-5"/>
      </button>
      <button v-else @click="backTodo(index)">
        <XMarkIcon class="h-4 w-5"/>
      </button>
      <button v-if="todo.editing" @click="editTodo(index)">
        <PencilIcon class="h-4 w-5"/>
      </button>
      <button v-else @click="editTodo(index)">
        <CheckIcon class="h-4 w-5"/>
      </button>
    </li>
  </ul>
  </div>
</template>

<style>
.done {
  text-decoration: line-through;
}

.center {
  margin: 0 auto;
}
</style>