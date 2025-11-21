<script setup>
  import { ref, onMounted, watch } from 'vue';
  import tickIcon from './assets/tick.svg';
  import crossIcon from './assets/cross.svg';

  // Состояние задачи
  const newTask = ref('');
  const tasks = ref([]);

  // Загружаем задачи из localStorage при старте
  onMounted(() => {
    const saved = localStorage.getItem("tasks");
    if (saved) tasks.value = JSON.parse(saved);
  });

  // Автосохранение в localStorage при любом изменении массива
  watch(tasks, () => {
    localStorage.setItem("tasks", JSON.stringify(tasks.value));
  }, { deep: true });


  // Добавление задачи
  function addTask() {
    if (!newTask.value.trim()) return;
    tasks.value.push({
      id: Date.now(),
      text: newTask.value.trim(),
      done: false,
    });
    newTask.value = "";
  }


  // Удаление задачи
  function deleteTask(id) {
    tasks.value = tasks.value.filter(task => task.id !== id);
  }


  // Отметка выполнено
  function toggleDone(id) {
    const task = tasks.value.find(task => task.id === id);
    if (task) task.done = !task.done;
  }

  // Проверка пустого списка
  const isEmpty = () => tasks.value.length === 0;
</script>

<template>
  <div class="container">
		<div class="todo">ToDo приложение</div>
		<div class="h4">список задач на сегодня</div>

		<!-- Form -->
		<div class="card" >
			<div class="card-header">Добавить новую задачу</div>
			<div class="card-body">
				<form @submit.prevent="addTask">
					<div class="form-group">
						<input type="text" class="form-control" v-model="newTask" placeholder="Текст задачи" required>
					</div>
					<button type="submit" class="btn-primary">Добавить</button>
				</form>
			</div>
		</div>

		<!-- List of tasks -->
    <div class="card">
      <ul class="list-group list-group-flush">
        <!-- Пустой список -->
        <li v-if="isEmpty()" class="list-group-item empty-list">
          <div class="empty-list__title">Список дел пуст</div>
        </li>

        <!-- Задачи -->
        <li v-for="task in tasks" :key="task.id" class="list-group-item d-flex justify-content-between task-item">
          <span :class="['task-title', task.done ? 'task-title--done' : '']">{{ task.text }}</span>
          <div class="task-item__buttons">
            <button type="button" class="btn-action" @click="toggleDone(task.id)">
              <img :src="tickIcon" alt="Done" width="18" height="18">
            </button>
            <button type="button" class="btn-action" @click="deleteTask(task.id)">
              <img :src="crossIcon" alt="Delete" width="18" height="18">
            </button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
  @import './style.css';
</style>
