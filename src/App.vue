<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import { TrashIcon, PencilIcon, CheckIcon, XMarkIcon} from '@heroicons/vue/24/solid'


let id = 0

const newTodo = ref('')
const todos = ref([])
const oldValue = ref('')
const filter = ref('')

const filteredTodos = computed(() => {
  switch(filter.value) {
    case 'all': return todos.value
    case 'active' : return todos.value.filter(todo => !todo.done)
    case 'completed' : return todos.value.filter(todo => todo.done)
  }
  return todos.value
})


function addTodo() {
  todos.value.push({ id: id++, text: newTodo.value, done: false, editing: true })
  newTodo.value = ''
}

function removeTodo(todo) {
  todos.value = todos.value.filter(t => t !== todo)
}

function editTodo(index) {
    todos.value[index].editing = !todos.value[index].editing
    oldValue.value = todos.value[index].text
}

function backTodo(index) {
  todos.value[index].editing = !todos.value[index].editing
   todos.value[index].text = oldValue.value
}

function checkTodo(index) {
  todos.value[index].done = !todos.value[index].done
}

function statusTodo(status) {
  filter.value = status      
}

watch(todos, () => {
  localStorage.setItem('todos', JSON.stringify(todos.value))
  // первым  арг название  arr вторым арг что сохранить
},
{deep: true}
)

onMounted(() => {
  const data = localStorage.getItem('todos')
  data ? todos.value = JSON.parse(data) : null
})

</script>

<template>
  <div class="center w-[500px] min-h-[700px] bg-white items-center rounded-lg p-10 border-4 border-sky-700">
  <h1 class="text-5xl font-bold text-sky-700 pb-8 text-center">Todo List</h1>
  <div class="flex justify-between font-bold text-xl">
    <button @click="statusTodo('all')" class="border-2 border-teal-400 text-sky-700 hover:bg-sky-700 hover:text-white hover:border-sky-700 active:border-white w-24 h-14 rounded-lg">All</button>
    <button @click="statusTodo('active')" class="border-2 border-teal-400 text-sky-700 hover:bg-sky-700 hover:text-white hover:border-sky-700 active:border-white w-24 h-14 rounded-lg">Active</button>
    <button @click="statusTodo('completed')" class="border-2 border-teal-400 text-sky-700 hover:bg-sky-700 hover:text-white hover:border-sky-700 active:border-white w-28 h-14 rounded-lg">Completed</button>
  </div>
  <form class="flex justify-between pt-10 text-sky-700" @submit.prevent="addTodo">
    <input class="border-2 border-sky-700 text-xl shadov h-14 p-2 outline-none rounded-lg" v-model="newTodo" required placeholder="new todo">
    <button class="font-bold text-xl border-2 border-teal-400 text-sky-700 hover:bg-sky-700 hover:text-white hover:border-sky-700 active:border-white w-28 h-14 rounded-lg">Add Todo</button>
  </form>
  <ul class="pt-10 grid gap-4 text-white">
    <li class="flex gap-3 text-xl bg-sky-700 p-2 rounded-lg" v-for="(todo, index) in filteredTodos" :key="todo.id">
      <input @click="checkTodo(index)" v-model="todo.done"  type="checkbox" class="w-5">
      <span v-if="todo.editing" :class="{ done: todo.done }">{{ todo.text }}</span>
      <input v-else v-model="todo.text" class="text-black outline-none rounded-lg border-2 p-1 border-sky-700 " required>
      <button v-if="todo.editing" @click="removeTodo(todo)">
        <TrashIcon class="h-5 w-6"/>
      </button>
      <button v-else @click="backTodo(index)">
        <XMarkIcon class="h-5 w-6"/>
      </button>
      <button v-if="todo.editing" @click="editTodo(index)">
        <PencilIcon class="h-5 w-6"/>
      </button>
      <button v-else @click="editTodo(index)">
        <CheckIcon class="h-5 w-6"/>
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
  margin-top: 6rem;
}

.shadov {
  box-shadow: inset 0.5px 0.5px 5px 5px #e2e8f0;
}

body {
  background-color: #49c5b6;
}
</style>