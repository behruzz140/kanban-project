<template>
  <div class="app-container" :class="{ 'dark-mode': darkMode }">
    <div class="header">
      <h1>Plus10 pro mini</h1>
      <div class="header-controls">
        <div class="search-container">
          <input type="text" v-model="searchText" placeholder="Search tasks..." class="search-input" />
        </div>
        <button @click="toggleTheme" class="theme-button">
          {{ darkMode ? '🌞' : '🌙' }}
        </button>
        <button @click="openNewTaskModal" class="new-task-button">
          + Yangi topshiriq
        </button>
      </div>
    </div>

    <div class="columns-container">
      <Column v-for="column in columns" :key="column.id" :column="column" :tasks="filterTasks(column.id)"
        @add-task="addNewTask" @delete-task="deleteTask" @drop-task="dropTask" @task-dragging="taskDragging" />
    </div>
    <div v-if="modalOpen" class="modal-overlay">
      <div class="modal-content">
        <h2>Yangi topshiriq yaratish</h2>
        <form @submit.prevent="saveNewTask">
          <div class="form-group">
            <label for="title">Sarlavha:</label>
            <input type="text" id="title" v-model="newTask.title" required class="form-input" />
          </div>
          <div class="form-group">
            <label for="description">Tavsif:</label>
            <textarea id="description" v-model="newTask.description" class="form-textarea"></textarea>
          </div>
          <div class="form-group">
            <label for="deadline">Muddati:</label>
            <input type="date" id="deadline" v-model="newTask.deadline" class="form-input" />
          </div>
          <div class="form-group">
            <label for="column">Ustun:</label>
            <select id="column" v-model="newTask.columnId" class="form-select">
              <option v-for="column in columns" :key="column.id" :value="column.id">
                {{ column.name }}
              </option>
            </select>
          </div>
          <div class="modal-buttons">
            <button type="button" @click="closeModal" class="cancel-button">Bekor qilish</button>
            <button type="submit" class="save-button">Saqlash</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import './header.css'
import { v4 as uuidv4 } from 'uuid';
import Column from '../column/column.vue';

export default {
  name: 'App',
  components: {
    Column
  },
  data() {
    return {
      columns: [
        { id: 'new', name: 'Yangi', color: 'blue' },
        { id: 'inProgress', name: 'Jarayonda', color: 'yellow' },
        { id: 'completed', name: 'Tugatilgan', color: 'green' }
      ],
      tasks: [],
      modalOpen: false,
      newTask: {
        title: '',
        description: '',
        deadline: '',
        columnId: 'new'
      },
      searchText: '',
      darkMode: false,
      draggingTask: null
    };
  },
  created() {
    const saved = localStorage.getItem('kanban-tasks');
    if (saved) {
      this.tasks = JSON.parse(saved);
    }
    const theme = localStorage.getItem('dark-mode');
    if (theme) {
      this.darkMode = JSON.parse(theme);
    }
  },
  methods: {
    openNewTaskModal() {
      this.modalOpen = true;
      this.newTask = {
        title: '',
        description: '',
        deadline: '',
        columnId: 'new'
      };
    },
    closeModal() {
      this.modalOpen = false;
    },
    saveNewTask() {
      const newTask = {
        id: uuidv4(),
        title: this.newTask.title,
        description: this.newTask.description,
        createdDate: new Date().toISOString(),
        deadline: this.newTask.deadline || null,
        columnId: this.newTask.columnId
      };

      this.tasks.push(newTask);
      this.saveToLocalStorage();
      this.closeModal();
    },
    addNewTask(columnId) {
      this.newTask.columnId = columnId;
      this.modalOpen = true;
    },
    deleteTask(taskId) {
      this.tasks = this.tasks.filter(task => task.id !== taskId);
      this.saveToLocalStorage();
    },
    taskDragging(task) {
      this.draggingTask = task;
    },
    dropTask(columnId) {
      if (this.draggingTask) {
        const taskIndex = this.tasks.findIndex(t => t.id === this.draggingTask.id);
        if (taskIndex !== -1) {
          this.tasks[taskIndex].columnId = columnId;
          this.saveToLocalStorage();
        }
        this.draggingTask = null;
      }
    },
    saveToLocalStorage() {
      localStorage.setItem('kanban-tasks', JSON.stringify(this.tasks));
    },
    filterTasks(columnId) {
      return this.tasks
        .filter(task => task.columnId === columnId)
        .filter(task => {
          if (!this.searchText) return true;
          return task.title.toLowerCase().includes(this.searchText.toLowerCase()) ||
            (task.description && task.description.toLowerCase().includes(this.searchText.toLowerCase()));
        });
    },
    toggleTheme() {
      this.darkMode = !this.darkMode;
      localStorage.setItem('dark-mode', JSON.stringify(this.darkMode));
    }
  }
};
</script>
