<template>
  <div id="app">
    <h1>To-Do List</h1>
    <input v-model="newTask" @keyup.enter="addTask" placeholder="Add a task">
    <ul>
      <li v-for="(task, index) in tasks" :key="index">
        {{ task }}
        <button @click="removeTask(index)">Delete</button>
      </li>
    </ul>
    <button @click="openCamera">Open Camera</button>
    <!-- display kar raha h camera user ko -->
    <video ref="video" width="300" height="200" autoplay></video>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      tasks: []
    }
  },
  methods: {
    addTask() {
      if (this.newTask.trim()) {
        this.tasks.push(this.newTask.trim());
        this.saveTasks();
        this.newTask = '';
      }
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
    loadTasks() {
      const savedTasks = localStorage.getItem('tasks');
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks);
      }
    },
    openCamera() {

      navigator.mediaDevices.getUserMedia({ video: true })
        .then(stream => {
          this.$refs.video.srcObject = stream;
        })
        .catch(error => {
          console.error("Error accessing camera: ", error);
        });
    }
  },
  created() {
    this.loadTasks();
  }
}
</script>
