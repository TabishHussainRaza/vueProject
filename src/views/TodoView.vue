<script setup>
import { ref, computed } from 'vue'

class Todo {
  constructor(id, text, done, date, priority) {
    this.id = id
    this.text = text
    this.done = done
    this.date = date
    this.priority = priority
  }
}

let id = 0
let sortOrderDate = ref('asc')
let sortOrderPriority = ref('asc')
const newTodo = ref('')
const newTodoDate = ref('')
const newTodoPriority = ref('')
let isSortByDate = ref(true)
let isSortByPriority = ref(false)
const hideCompleted = ref(false)
const hideNullPriority = ref(false)

const todos = ref([
  new Todo(id++, 'Learn HTML', true, '2023-12-01'),
  new Todo(id++, 'Learn JavaScript', false, '2023-12-01'),
  new Todo(id++, 'Learn Vue', true, '2023-12-01')
])

function addTodo() {
  todos.value.push(new Todo(id++, newTodo.value, false, newTodoDate.value, newTodoPriority.value))
  newTodo.value = ''
  newTodoDate.value = ''
}

function removeTodo(todo) {
  todos.value = todos.value.filter((t) => t !== todo)
}

const filteredTodos = computed(() => {
  let current = [...todos.value];

  if (hideCompleted.value) {
    current = current.filter((t) => !t.done);
  }

  if (hideNullPriority.value) {
    current = current.filter((t) => t.priority != null);
  }

  return current;
})

const sortedTodos = computed(() => {
  const sorted = [...filteredTodos.value];
  if (isSortByDate.value) {
    sorted.sort((a, b) => {
      const dateA = new Date(a.date);
      const dateB = new Date(b.date);
      return sortOrderDate.value === 'asc' ? dateA - dateB : dateB - dateA;
    });
  } else if (isSortByPriority.value) {
    sorted.sort((a, b) => {
      const priorityOrder = { 'low': 1, 'medium': 2, 'high': 3 };
      const priorityA = a.priority in priorityOrder ? priorityOrder[a.priority] : 0;
      const priorityB = b.priority in priorityOrder ? priorityOrder[b.priority] : 0;
      return sortOrderPriority.value === 'asc' ? priorityA - priorityB : priorityB - priorityA;
    });
  }
  return sorted;
})

function toggleSortOrder(filter) {
  if (filter === "date") {
    isSortByDate.value = true;
    isSortByPriority.value = false;
    sortOrderDate.value = sortOrderDate.value === 'asc' ? 'desc' : 'asc';
  } else {
    isSortByDate.value = false;
    isSortByPriority.value = true;
    sortOrderPriority.value = sortOrderPriority.value === 'asc' ? 'desc' : 'asc';
  }
}

</script>

<template>
  <div class="todo">
    <form @submit.prevent="addTodo">
      <label for="task">Task:</label>
      <br/>
      <input id="task" v-model="newTodo" placeholder="Add new todo" class="inputBox">
      <br/>
      <label for="task">Date:</label>
      <br/>
      <input id="date" type="date" v-model="newTodoDate" class="inputBox">
      <br/>
      <label for="prioritySelect">Select Priority:</label>
      <br/>
      <select id="prioritySelect" class="inputBox" v-model="newTodoPriority">
        <option value="high">High</option>
        <option value="medium">Medium</option>
        <option value="low">Low</option>
      </select>
      <br/>
      <button>Add Todo</button>    
    </form>
    <button @click="toggleSortOrder('date')">Sort by Date {{ sortOrderDate.toUpperCase() }}</button>&nbsp;
    <button @click="toggleSortOrder('priority')">Sort by Priority {{ sortOrderPriority.toUpperCase() }}</button>&nbsp;
    <button @click="hideCompleted = !hideCompleted">
    {{ hideCompleted ? 'Show all Tasks' : 'Hide completed Tasks' }}
    </button>&nbsp;
    <button @click="hideNullPriority = !hideNullPriority">
    {{ hideNullPriority ? 'Show All Tasks Including Null Priority' : 'Hide Tasks with Null Priority' }}
    </button>
    <br/>
    <table>
      <tr>
        <th>Task</th>
        <th>Date</th>
        <th>Priority</th>
        <th>Status</th>
        <th>Action</th>
      </tr>
      <tr :class="{ done: todo.done }" v-for="todo in sortedTodos" :key="todo.id">
        <td>{{ todo.text }}</td>
        <td>{{ todo.date }}</td>
        <td>{{ todo.priority }}</td>
        <td><input type="checkbox" v-model="todo.done"></td>
        <td><button @click="removeTodo(todo)">X</button></td>
      </tr>
    </table>
    
  </div>
  
</template>

<style>
  table{
    border-collapse: collapse;
    border: 1px solid;
    width: 100%;
  }
  td, th{
    padding: 5px;
    border: 1px solid;
  }
  .done {
    text-decoration: line-through;
  }
  .todo {
    max-width: 400px;
    margin: auto;
  }

  form {
    margin-bottom: 20px;
  }

  .inputBox {
    width: 70%;
    padding: 8px;
    margin-right: 10px;
  }

  button {
    padding: 8px 12px;
    background-color: #4caf50;
    color: #fff;
    border: none;
    cursor: pointer;
    margin-top: 10px;
    margin-bottom: 10px;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    border: 1px solid #ddd;
    margin-bottom: 8px;
  }
</style>



var text = 0;

fucntion y(){
 
}