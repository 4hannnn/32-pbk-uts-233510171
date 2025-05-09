<!-- src/App.vue -->
<template>
  <div class="container">
    <h1>Manajemen Kegiatan Saya</h1>

    <ul>
      <li v-for="task in tasks" :key="task.id">
        {{ task.text }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [
        { id: 1, text: 'Contoh Kegiatan' }
      ]
    }
  }
}
</script>

