  <template>
    <div class="task" :class="{ 'past-deadline': isPastDeadline }" draggable="true" @dragstart="dragStart">
      <div class="task-header">
        <h3>{{ task.title }}</h3>
        <button @click="deleteTask" class="delete-button">×</button>
      </div>

      <p v-if="task.description" class="task-description">{{ task.description }}</p>

      <div class="task-footer">
        <div class="task-meta">
          <div class="task-date">
            <span class="date-label">Yaratilgan:</span>
            {{ formatDate(task.createdDate) }}
          </div>

          <div v-if="task.deadline" class="task-deadline" :class="{ 'urgent': isDeadlineNear }">
            <span class="date-label">Muddati:</span>
            {{ formatDate(task.deadline) }}
          </div>
        </div>
      </div>
    </div>
  </template>

<script>
import './task.css'
export default {
  name: 'Task',
  props: {
    task: {
      type: Object,
      required: true
    }
  },
  computed: {
    isPastDeadline() {
      if (!this.task.deadline) return false;
      const today = new Date();
      const deadline = new Date(this.task.deadline);
      return deadline < today;
    },
    isDeadlineNear() {
      if (!this.task.deadline) return false;
      const today = new Date();
      const deadline = new Date(this.task.deadline);

      const diff = Math.floor((deadline - today) / (1000 * 60 * 60 * 24));
      return diff >= 0 && diff <= 3;
    }
  },
  methods: {
    formatDate(dateString) {
      const date = new Date(dateString);
      return date.toLocaleDateString('en-US', {
        year: 'numeric',
        month: 'short',
        day: 'numeric'
      });
    },
    deleteTask() {
      this.$emit('delete-task', this.task.id);
    },
    dragStart(event) {
      this.$emit('task-dragging', this.task);
      event.dataTransfer.effectAllowed = 'move';
      event.dataTransfer.setData('text/plain', this.task.id);
      const dragImage = event.target.cloneNode(true);
      dragImage.style.position = 'absolute';
      dragImage.style.top = '-1000px';
      document.body.appendChild(dragImage);
      event.dataTransfer.setDragImage(dragImage, 20, 20);

      setTimeout(() => {
        document.body.removeChild(dragImage);
      }, 0);
    }
  }
};
</script>
