<template>
  <div class="container">
    <div class="status">
      <h2>
        <label class="status-label not-started">Not started</label>
        <span style="color: #B9B9B7;">{{ taskCounts.notStarted }}</span>
        <span class="icon-container">
          <i class="fas fa-ellipsis-h"></i>
          <i class="fas fa-plus"></i>
        </span>
      </h2>
      <div class="tasks" id="notStarted" @drop="drop($event, 'notStarted')" @dragover.prevent="allowDrop">
        <div class="task" v-for="task in tasks.notStarted" :key="task.id" :draggable="true" @dragstart="dragStart($event, 'notStarted', task.id)" @click="showDetail(task)">
          <p>{{ task.description }}</p>
        </div>
      </div>
      <button @click="addTask('notStarted')"><span>+ New</span></button>
    </div>
    <div class="status">
      <h2>
        <label class="status-label in-progress">In progress</label>
        <span style="color: #B9B9B7;">{{ taskCounts.inProgress }}</span>
        <span class="icon-container">
          <i class="fas fa-ellipsis-h"></i>
          <i class="fas fa-plus"></i>
        </span>
      </h2>
      <div class="tasks" id="inProgress" @drop="drop($event, 'inProgress')" @dragover.prevent="allowDrop">
        <div class="task" v-for="task in tasks.inProgress" :key="task.id" :draggable="true" @dragstart="dragStart($event, 'inProgress', task.id)" @click="showDetail(task)">
          <p>{{ task.description }}</p>
        </div>
      </div>
      <button @click="addTask('inProgress')"><span>+ New</span></button>
    </div>
    <div class="status">
      <h2>
        <label class="status-label completed">Completed</label>
        <span style="color: #B9B9B7;">{{ taskCounts.completed }}</span>
        <span class="icon-container">
          <i class="fas fa-ellipsis-h"></i>
          <i class="fas fa-plus"></i>
        </span>
      </h2>
      <div class="tasks" id="completed" @drop="drop($event, 'completed')" @dragover.prevent="allowDrop">
        <div class="task" v-for="task in tasks.completed" :key="task.id" :draggable="true" @dragstart="dragStart($event, 'completed', task.id)" @click="showDetail(task)">
          <p>{{ task.description }}</p>
        </div>
      </div>
      <button @click="addTask('completed')"><span>+ New</span></button>
    </div>

    <div v-if="selectedTask" class="detail-view">
      <h2>Task Details</h2>

      <div>
        <label>Status: </label>
        <select v-model="selectedTask.status">
          <option value="notStarted">Not started</option>
          <option value="inProgress">In progress</option>
          <option value="completed">Completed</option>
        </select>
      </div>
      <div>
        <label>Description: </label>
        <textarea v-model="selectedTask.description"></textarea>
      </div>
      <div v-if="errorMessage" class="error-message">
      {{ errorMessage }}
    </div>
      <button @click="saveTask">Save</button>
      <button @click="deleteTask">Delete</button>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: {
        notStarted: [
          { id: 1, description: "Second round interview" },
          { id: 2, description: "Internship in airtribe" },
          { id: 3, description: "Full time employee" }
        ],

        inProgress: [
          { id: 4, description: "Interview In progress" },
          { id: 5, description:  "Project In progress" }
        ],

        completed: [
          { id: 6, description: "Application Completed" },
          { id: 7, description: "Project Completed" }
        ]
      },
      selectedTask: null, 
      errorMessage: "" 
    };
  },
  methods: {
    addTask(status) {
      const description = prompt("Enter task description:");
      if (description) {
        const newTask = { id: Date.now(), description };
        this.tasks[status].push(newTask);
      }
    },
    allowDrop(event) {
      event.preventDefault();
    },
    dragStart(event, status, taskId) {
      event.dataTransfer.setData("text/plain", JSON.stringify({ status, taskId }));
    },
    drop(event, newStatus) {
      event.preventDefault();
      const { status, taskId } = JSON.parse(event.dataTransfer.getData("text/plain"));
      const taskIndex = this.tasks[status].findIndex(task => task.id === taskId);
      const task = this.tasks[status].splice(taskIndex, 1)[0];
      task.id = Date.now(); 
      this.tasks[newStatus].push(task);
    },
    showDetail(task) {
      this.selectedTask = task;
    },
    saveTask() {
      this.selectedTask = null; 
    },
    deleteTask() {
      if (!this.selectedTask) {
        console.error("No task selected for deletion.");
        this.errorMessage = "No task selected for deletion.";
        return;
      }

      const { status } = this.selectedTask;
      if (!status || !this.tasks[status]) {
        console.error("Invalid task status or task list.");
        this.errorMessage = "Invalid task status or task list.";
        return;
      }

      const index = this.tasks[status].findIndex(task => task.id === this.selectedTask.id);
      if (index !== -1) {
        this.tasks[status].splice(index, 1);
        this.selectedTask = null; 
        this.errorMessage = ""; 
      } else {
        this.errorMessage = "Task not found for deletion.";
      }
    }
  },
  computed: {
    taskCounts() {
      return {
        notStarted: this.tasks.notStarted.length,
        inProgress: this.tasks.inProgress.length,
        completed: this.tasks.completed.length
      };
    }
  }
};
</script>

<style scoped>

@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css');


@import url('../css/index.css');

.error-message {
  color: red;
  margin-top: 10px;
}

</style>
