<!-- src/App.vue -->
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
      <button @click="addTask" class="btn add-btn">
        <span class="btn-text">Tambah</span>
        <span class="btn-icon">+</span>
      </button>
    </div>

    <div class="filter-container">
      <button @click="showAll = true" :class="{ active: showAll }" class="filter-btn">
        Semua Kegiatan
      </button>
      <button @click="showAll = false" :class="{ active: !showAll }" class="filter-btn">
        Belum Selesai
      </button>
    </div>

    <div v-if="filteredTasks.length === 0" class="empty-state">
      Belum ada kegiatan untuk ditampilkan
    </div>

    <transition-group name="task-list" tag="ul" class="task-list">
      <li v-for="(task, index) in filteredTasks" :key="task.id" class="task-item"
        :class="{ completed: task.completed }">
        <div class="task-content">
          <label class="checkbox-container">
            <input type="checkbox" :checked="task.completed" @change="toggleComplete(task.id)" />
            <span class="checkmark"></span>
          </label>
          <span class="task-text">{{ task.text }}</span>
        </div>
        <button @click="deleteTask(task.id)" class="btn delete-btn">Hapus</button>
      </li>
    </transition-group>

    <div class="stats">
      <div class="stat-card">
        <div class="stat-title">Total Kegiatan</div>
        <div class="stat-value">{{ tasks.length }}</div>
      </div>
      <div class="stat-card">
        <div class="stat-title">Selesai</div>
        <div class="stat-value">{{ completedCount }}</div>
      </div>
      <div class="stat-card">
        <div class="stat-title">Belum Selesai</div>
        <div class="stat-value">{{ tasks.length - completedCount }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [],
      newTask: '',
      showAll: true,
      currentYear: new Date().getFullYear(),
      isDarkMode: true,
      theme: 'dark'
    }
  },
  computed: {
    filteredTasks() {
      if (this.showAll) {
        return this.tasks
      } else {
        return this.tasks.filter(task => !task.completed)
      }
    },
    completedCount() {
      return this.tasks.filter(task => task.completed).length
    }
  },
  methods: {
    generateId() {
      return Date.now().toString(36) + Math.random().toString(36).substr(2);
    },
    addTask() {
      if (this.newTask.trim()) {
        this.tasks.unshift({
          id: this.generateId(),
          text: this.newTask.trim(),
          completed: false,
          createdAt: new Date()
        })
        this.newTask = ''
        this.saveToLocalStorage()
      }
    },
    deleteTask(id) {
      const index = this.tasks.findIndex(task => task.id === id)
      if (index !== -1) {
        this.tasks.splice(index, 1)
        this.saveToLocalStorage()
      }
    },
    toggleComplete(id) {
      const index = this.tasks.findIndex(task => task.id === id)
      if (index !== -1) {
        this.tasks[index].completed = !this.tasks[index].completed

        
        if (this.tasks[index].completed) {
          const completedTask = this.tasks.splice(index, 1)[0]
          this.tasks.push(completedTask)
        }

        this.saveToLocalStorage()
      }
    },
    saveToLocalStorage() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks))
    },
    loadFromLocalStorage() {
      const savedTasks = localStorage.getItem('tasks')
      if (savedTasks) {
        try {
          const parsedTasks = JSON.parse(savedTasks)


          const incompleteTasks = parsedTasks.filter(task => !task.completed)
          const completedTasks = parsedTasks.filter(task => task.completed)

          this.tasks = [...incompleteTasks, ...completedTasks]
        } catch (e) {
          console.error('Error parsing tasks from localStorage:', e)
          this.tasks = []
        }
      }
    },
    toggleTheme() {
      this.theme = this.isDarkMode ? 'dark' : 'light'
      document.documentElement.setAttribute('data-theme', this.theme)
      localStorage.setItem('theme', this.theme)
    },
    loadThemePreference() {
      const savedTheme = localStorage.getItem('theme') || 'dark'
      this.theme = savedTheme
      this.isDarkMode = savedTheme === 'dark'
      document.documentElement.setAttribute('data-theme', savedTheme)
    }
  },
  mounted() {
    this.loadFromLocalStorage()
    this.loadThemePreference()


    this.$nextTick(() => {
      const inputEl = document.querySelector('input[type="text"]')
      if (inputEl) {
        inputEl.focus()
      }
    })
  }
}
</script>

<style>
.task-list-enter-active,
.task-list-leave-active {
  transition: all 0.5s;
}

.task-list-enter-from,
.task-list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

.task-list-move {
  transition: transform 0.5s;
}
</style>