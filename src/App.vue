<template>
  <div class="container">
    <h1>Manajemen Kegiatan Saya</h1>

    <div class="add-task">
      <input type="text" v-model="newTask" placeholder="Apa yang ingin Anda lakukan?" @keyup.enter="addTask" />
      <button @click="addTask">Tambah</button>
    </div>

    <ul>
      <li v-for="task in tasks" :key="task.id">
        {{ task.text }}
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
      tasks: []
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
    }
  }
}
</script>
