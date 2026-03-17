<script setup lang="ts">
import { ref, computed } from 'vue';

// Define types Todo and Priority
type Priority = 'low' | 'medium' | 'high';
type Todo = {
  id: number;
  text: string;
  priority: Priority;
  completed: boolean;
}
const newTodo = {
  id: Date.now(),
  text: '',
  priority: 'low' as Priority,
  completed: false
}
// Create refs todos,
const todos = ref<Todo[]>([]);
const newPriority = ref<Priority>('low');
const sortOption = ref<'priority' | 'time'>('time');
const showCompleted = ref<boolean>(true);
  //focus ref
  const todoInput = ref<HTMLInputElement | null>(null);


// Create sort function with completed filter: by priority and time
const filteredAndSortedTodos = computed(() => {
  let result = showCompleted.value
    ? todos.value
    : todos.value.filter((todo) => !todo.completed);

  if (sortOption.value === 'priority') {
    result.sort((a, b) => {
      const priorityOrder = ['low', 'medium', 'high'];
      return priorityOrder.indexOf(b.priority) - priorityOrder.indexOf(a.priority);
    });
  } else {
    result.sort((a, b) => b.id - a.id);
  }
  return result;
})

// Create addTodo function
const addTodo = () => {
  if (!newTodo.text.trim()) {
    todoInput.value?.focus();
    return;// prevent adding empty todo
  }
  newTodo.id = Date.now();
  newTodo.priority = newPriority.value;
  newTodo.text = newTodo.text.trim();
  todos.value.push({
    ...newTodo
  });
  newTodo.text = '';
  newPriority.value = 'low';
  todoInput.value?.focus();
}

//Delete todo
const deleteTodo = (id: number) => {
  todos.value = todos.value.filter((todo) => todo.id !== id);
}
//Clear completed
const clearCompleted = () => {
  todos.value = todos.value.filter(todo => !todo.completed);
}


</script>

<template>
  <h1> To-do with Priority Tracker</h1>
  <div class="input-container">
    <!-- Input form - new todo -->
    <label for="new-todo-name">New To-do:
      <input type="text" id="new-todo-name" v-model="newTodo.text" ref="todoInput"/>
    </label>
    <label for="new-todo-priority">Priority:
      <select id="new-todo-priority" v-model="newPriority">
        <option value="low">Low</option>
        <option value="medium">Medium</option>
        <option value="high">High</option>
      </select>
    </label>
    <button @click="addTodo">Add</button>
  </div>

  <!-- sort by priority or time (option for show/hide completed)-->
  <div class="sort-container">
    <label for="sort-option">Sort by:
      <input type="radio" id="sort-priority" name="sort-option" value="priority" v-model="sortOption" />
      <label for="sort-priority">Priority</label>
      <input type="radio" id="sort-time" value="time" name="sort-option" v-model="sortOption" />
      <label for="sort-time">Time</label>
    </label>

    <!-- Show/hide completed -->
    <label for="show-completed">
      <input type="checkbox" id="show-completed" v-model="showCompleted">
      Show Completed
    </label>
  </div>

  <!-- Todo list (item, time, priority) with completed, delete button  -->
  <div class="todo-list">
    <li v-for="todo in filteredAndSortedTodos" :key="todo.id">
      <div class="todo-left">
        <input type="checkbox" v-model="todo.completed">
        <span>{{ todo.text }}</span>
      </div>

      <div class="todo-right">
        <span>{{ todo.priority }}</span>
        <button @click="deleteTodo(todo.id)">Delete</button>
      </div>
    </li>
  </div>
  <button @click="clearCompleted" class="clear-button">Clear Completed</button>
</template>
