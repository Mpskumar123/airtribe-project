<template>
  <div class="container">
    <div class="status">
      <h2>
        <label style="background-color: #ffcdd2; border-radius: 5px;">Not started</label>
        <span style="color: #B9B9B7;">{{ taskCounts.notStarted }}</span>
         <i class="fas fa-ellipsis-h" style="color: #B9B9B7;"></i> 
        <i class="fas fa-plus"  style="color: #B9B9B7;"></i> 
      </h2>
      <div class="tasks" id="notStarted" @drop="drop($event, 'notStarted')" @dragover.prevent="allowDrop">
        <div class="task" v-for="task in tasks.notStarted" :key="task.id" :draggable="true" @dragstart="dragStart($event, 'notStarted', task.id)">
          {{ task.description }}
        </div>
      </div>
      <button @click="addTask('notStarted')">+ New</button>
    </div>
    <div class="status">
      <h2>
        <label style="background-color: #efe3c3; border-radius: 5px;">In progress</label>
        <span style="color: #B9B9B7;">{{ taskCounts.inProgress }}</span>
        <i class="fas fa-ellipsis-h" style="color: #B9B9B7;"></i> 
        <i class="fas fa-plus" style="color: #B9B9B7;"></i> 
      </h2>
      <div class="tasks" id="inProgress" @drop="drop($event, 'inProgress')" @dragover.prevent="allowDrop">
        <div class="task" v-for="task in tasks.inProgress" :key="task.id" :draggable="true" @dragstart="dragStart($event, 'inProgress', task.id)">
          {{ task.description }}
        </div>
      </div>
      <button @click="addTask('inProgress')">+ New</button>
    </div>
    <div class="status">
      <h2>
        <label style="background-color: #cde7e2; border-radius: 5px;">Completed</label>
       <span style="color: #B9B9B7;">{{ taskCounts.completed }}</span>
        <i class="fas fa-ellipsis-h" style="color: #B9B9B7;"></i> 
        <i class="fas fa-plus" style="color: #B9B9B7;"></i> 
      </h2>
      <div class="tasks" id="completed" @drop="drop($event, 'completed')" @dragover.prevent="allowDrop">
        <div class="task" v-for="task in tasks.completed" :key="task.id" :draggable="true" @dragstart="dragStart($event, 'completed', task.id)">
          {{ task.description }}
        </div>
      </div>
      <button @click="addTask('completed')">+ New</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: {
        notStarted: [
          { id: 1, description: "Task 1 - Not started" },
          { id: 2, description: "Task 2 - Not started" },
          { id: 3, description: "Task 3 - Not started" }
        ],
        inProgress: [{ id: 4, description: "Task 4 - In progress" }],
        completed: [{ id: 5, description: "Task 5 - Completed" }]
      }
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
      task.id = Date.now(); // Update task ID
      this.tasks[newStatus].push(task);
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



<style>
@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css');


@import url('../css/index.css');

</style>