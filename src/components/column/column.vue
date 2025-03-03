<template>
  <div class="column" :class="{ 'accepting-drop': acceptingDrop }" @dragover.prevent="dragOver" @dragleave="dragLeave"
    @drop.prevent="drop">
    <div class="column-header" :style="{ backgroundColor: columnColor }">
      <h2>{{ column.name }}</h2>
      <span class="task-count">{{ tasks.length }}</span>
      <button @click="addNewTask" class="add-button">+</button>
    </div>
    <div class="tasks-container">
      <Task v-for="task in tasks" :key="task.id" :task="task" @delete-task="deleteTask" @task-dragging="taskDragging" />
      <div v-if="tasks.length === 0" class="empty-message">
        Bu ustunda vazifa yo'q
      </div>
    </div>
  </div>
</template>

<script>
import Task from '../task-component/tasks.vue';
import './column.css'
export default {
  name: 'Column',
  components: {
    Task
  },
  props: {
    column: {
      type: Object,
      required: true
    },
    tasks: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      acceptingDrop: false
    };
  },
  computed: {
    columnColor() {
      const colors = {
        blue: '#4a6bff',
        yellow: '#ffb347',
        green: '#4caf50'
      };
      return colors[this.column.color] || '#4a6bff';
    }
  },
  methods: {
    addNewTask() {
      this.$emit('add-task', this.column.id);
    },
    deleteTask(taskId) {
      this.$emit('delete-task', taskId);
    },
    taskDragging(task) {
      this.$emit('task-dragging', task);
    },
    dragOver(event) {
      this.acceptingDrop = true;
    },
    dragLeave() {
      this.acceptingDrop = false;
    },
    drop(event) {
      this.acceptingDrop = false;
      this.$emit('drop-task', this.column.id);
    }
  }
};
</script>
