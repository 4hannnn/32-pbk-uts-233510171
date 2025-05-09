<template>
    <div class="container">
    <div class="theme-switch-wrapper">
      <label class="theme-switch">
        <input type="checkbox" v-model="isDarkMode" @change="toggleTheme">
        <span class="slider round"></span>
      </label>
      <span class="theme-label">{{ isDarkMode ? '🌙' : '☀️' }}</span>
    </div>


    <h1>Manajemen Kegiatan Saya</h1>

    <div class="add-task">
      <input type="text" v-model="newTask" placeholder="Apa yang ingin Anda lakukan?" @keyup.enter="addTask" />
      <button @click="addTask">Tambah</button>
    </div>

    <div>
      <button @click="showAll = true">Semua</button>
      <button @click="showAll = false">Belum Selesai</button>
    </div>

    <ul>
      <li v-for="task in filteredTasks" :key="task.id">
        <input type="checkbox" :checked="task.completed" @change="toggleComplete(task.id)" />
        <span :style="{ textDecoration: task.completed ? 'line-through' : 'none' }">
          {{ task.text }}
        </span>
        <button @click="deleteTask(task.id)">Hapus</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      tasks: [],
      showAll: true,
      isDarkMode: false
    }
  },
  computed: {
    filteredTasks() {
      return this.showAll ? this.tasks : this.tasks.filter(task => !task.completed)
    }
  },
  methods: {
    generateId() {
      return Date.now().toString(36) + Math.random().toString(36).substr(2)
    },
    addTask() {
      if (this.newTask.trim()) {
        this.tasks.unshift({
          id: this.generateId(),
          text: this.newTask.trim(),
          completed: false
        })
        this.newTask = ''
      }
    },
    deleteTask(id) {
      const index = this.tasks.findIndex(task => task.id === id)
      if (index !== -1) {
        this.tasks.splice(index, 1)
      }
    },
    toggleComplete(id) {
      const task = this.tasks.find(task => task.id === id)
      if (task) {
        task.completed = !task.completed
      }
    }
  }
}
</script>
