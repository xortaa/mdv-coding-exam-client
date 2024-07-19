<script>
import TaskManager from "./components/TaskManager.vue"

export default {
  components: {
    TaskManager,
  },
}
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">Simple Task Manager</h2>
    </section>

    <TaskManager />
  </main>
</template>
