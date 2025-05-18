<template>
  <div class="max-w-md mx-auto p-6 bg-white rounded-lg shadow-md">
    <h1 class="text-2xl font-bold text-gray-800 mb-4">Todo List</h1>

    <div class="mb-6">
      <form class="flex gap-2" @submit="HandleSubmit">
        <input type="text" placeholder="Add a new task..."
          class="flex-1 px-4 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-primary focus:border-transparent"
          v-model="taskAdded" />
        <button type="submit"
          class="px-4 py-2 bg-purple-600 text-white font-medium rounded-md hover:bg-purple-700 transition-colors">
          Add
        </button>
      </form>
    </div>
    <p v-if="arr.length == 0" class="text-gray-400">You don't have any listed Tasks, Add new one!</p>
    <ul class="space-y-2">
      <li v-for="item in arr" :key="item.id"
        class="flex items-center justify-between p-3 bg-gray-50 rounded-md border border-gray-200 group hover:bg-gray-100 transition-colors">
        <div class="flex items-center gap-3">
          <input type="checkbox" :id="`task-${item.id}`" :checked="item.status == 'completed'"
            @change="changeStatue(item.id, $event.target.checked)"
            class="w-5 h-5 rounded-md text-purple-400 focus:ring-purple-400"/>
          <label :for="`task-${item.id}`" :class="[
            'cursor-pointer text-gray-800',
            item.status === 'completed' ? 'line-through' : '',
            item.status === 'completed' ? 'text-gray-600' : ''
          ]">
            {{ item.text }}
          </label>
        </div>
        <button class="text-red-500 opacity-0 group-hover:opacity-100 transition-opacity" aria-label="Delete task" @click="removeTask(item.id)">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 24 24" fill="none" stroke="currentColor"
            stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M3 6h18"></path>
            <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6"></path>
            <path d="M8 6V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
          </svg>
        </button>
      </li>
    </ul>

    <div class="mt-4 text-sm text-gray-500 flex justify-between">
      <span>{{counterOfCompletedTasks}} of {{conterOfAllTasks}} completed</span>
      <button class="text-primary hover:underline" @click="deleteAllCompletedTasks">
        Clear completed
      </button>
    </div>
    <p v-if="alert" class="text-green-400 text-sm font-bold">{{ alert }}</p>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const alert = ref("");
const arr = ref([]);
const storedArr = localStorage.getItem('arr');
const taskAdded = ref("");
const idd = ref(1);
const counterOfCompletedTasks = computed(() => 
  arr.value.filter(item => item.status === "completed").length
);
const conterOfAllTasks = computed(() => 
  arr.value.length
);

if (storedArr) {
  arr.value = JSON.parse(storedArr);
}

function showAlert(message) {
  alert.value = message

  setTimeout(() => {
    alert.value = ''
  }, 2000)
}

const HandleSubmit = (e) => {
  e.preventDefault();
  if (taskAdded.value.trim()) {
    arr.value.push({ id: idd.value, text: taskAdded.value.trim(), status: "pending" });
    localStorage.setItem('arr', JSON.stringify(arr.value));
    idd.value++;
    taskAdded.value = "";
    showAlert("Task added successfuly!")
  }
}

const changeStatue = (id, isChecked) => {
  const item = arr.value.find(item => item.id === id);
  item.status = isChecked ? "completed" : "pending"
  localStorage.setItem('arr', JSON.stringify(arr.value));
  showAlert("status changed successfuly");
}

const removeTask = (id) => {
  arr.value = arr.value.filter(item => item.id !== id);
  localStorage.setItem('arr', JSON.stringify(arr.value));
  showAlert("Task Deleted Successfuly!");
}

const deleteAllCompletedTasks = () => {
  arr.value = arr.value.filter(item => item.status !== 'completed');
  localStorage.setItem('arr', JSON.stringify(arr.value));
  showAlert("you have delete all completed tasks");
}
</script>