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
    <button v-if="cameraOpen" @click="closeCamera">Close Camera</button>
    <!-- display kar raha h camera user ko -->
    <video ref="video" width="300" height="200" autoplay></video>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      tasks: [],
      cameraOpen: false,
      stream: null
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
          this.stream = stream;
          this.$refs.video.srcObject = stream;
          this.cameraOpen = true;
        })
        .catch(error => {
          console.error("Error accessing camera: ", error);
        });
    },
    closeCamera() {
      if (this.stream) {
        const tracks = this.stream.getTracks();
        tracks.forEach(track => track.stop());
        this.$refs.video.srcObject = null;
        this.cameraOpen = false;
        this.stream = null;
      }
    }
  },
  created() {
    this.loadTasks();
  }
}
</script>
