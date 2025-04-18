<template>
  <div class="min-h-screen bg-gray-100 flex flex-col items-center p-4">
    <h1 class="text-3xl font-bold mb-6 mt-5">📋 Todo App</h1>

    <div class="w-full max-w-md">
      <form @submit.prevent="addTodo" class="flex gap-2 mb-4">
        <input
          v-model="newTodo"
          type="text"
          placeholder="Tambah todo..."
          class="flex-1 p-2 rounded-lg border border-gray-300 focus:ring-2 focus:ring-blue-400"
        />
        <button type="submit" class="bg-blue-500 text-white px-4 rounded-lg hover:bg-blue-600">
          Add
        </button>
      </form>

      <ul class="space-y-2">
        <li v-for="(todo, index) in todos" :key="index" class="flex items-center justify-between bg-white p-3 rounded-lg shadow">
          <div class="flex items-center gap-2 w-full">
            <input type="checkbox" v-model="todo.done" class="w-5 h-5 text-blue-500 focus:ring-blue-400">
            <template v-if="editIndex === index">
              <input
                v-model="editText"
                @keyup.enter="saveEdit(index)"
                @blur="saveEdit(index)"
                class="flex-1 p-1 border-b border-gray-300 focus:outline-none focus:border-blue-400"
              />
            </template>
            <template v-else>
              <span @click="startEdit(index, todo.text)" :class="{ 'line-through text-gray-400': todo.done }" class="flex-1 cursor-pointer">
                {{ todo.text }}
              </span>
            </template>
          </div>
          <button @click="removeTodo(index)" class="text-red-500 hover:text-red-700">❌</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts" setup>
import {
  onMounted,
  watch,
  ref
} from 'vue';

type Todo = {
  text: string
  done: boolean
}

const todos = ref<Todo[]>([]);
const newTodo = ref<string>('');

const addTodo = () => {
  if (newTodo.value.trim() !== '') {
    todos.value.push({ text: newTodo.value.trim(), done: false });
    newTodo.value = '';
  }
};
const removeTodo = (index: number) => {
  todos.value.splice(index, 1);
};

const loadTodos = () => {
  const savedTodos = localStorage.getItem('todos');
  if (savedTodos) {
    todos.value = JSON.parse(savedTodos);
  }
};

const saveTodos = () => {
  localStorage.setItem('todos', JSON.stringify(todos.value));
};
watch(todos, saveTodos, { deep: true });

const editIndex = ref<number | null>(null);
const editText = ref<string>('');

const startEdit = (index: number, text: string) => {
  editIndex.value = index;
  editText.value = text;
};

const saveEdit = (index: number) => {
  if (editText.value.trim() !== '') {
    todos.value[index].text = editText.value.trim();
  }
  editIndex.value = null;
  editText.value = '';
};

onMounted(() => {
  loadTodos();
});
</script>
