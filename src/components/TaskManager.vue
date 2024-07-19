<script lang="ts">
import { defineComponent, ref, computed, watch, onMounted } from "vue"
import axios from "axios"

interface Task {
  id?: number
  title: string
  completed: boolean
}

export default defineComponent({
  name: "App",
  setup() {
    const tasks = ref<Task[]>([])
    const input_title = ref<string>("")

    const fetchTasks = async () => {
      try {
        const response = await axios.get("http://localhost:5000")
        tasks.value = response.data
      } catch (error) {
        console.error("There was an error fetching the tasks:", error)
      }
    }

    watch(
      tasks,
      (newVal) => {
        localStorage.setItem("tasks", JSON.stringify(newVal))
      },
      { deep: true }
    )

    const addTask = async () => {
      if (input_title.value.trim() === "") {
        return
      }

      try {
        const response = await axios.post("http://localhost:5000", {
          title: input_title.value,
          completed: false,
        })
        console.log(response.data)
        fetchTasks()
      } catch (error) {
        console.error("There was an error adding the task:", error)
      }
    }

    const toggleComplete = async (task: Task) => {
      if (!task.id) return
      try {
        await axios.put(`http://localhost:5000/${task.id}/toggle-complete`)
        fetchTasks() 
      } catch (error) {
        console.error("There was an error updating the task:", error)
      }
    }

    const deleteTask = async (task: Task) => {
      if (!task.id) return
      try {
        await axios.delete(`http://localhost:5000/${task.id}/delete`)
        fetchTasks() 
      } catch (error) {
        console.error("There was an error deleting the task:", error)
      }
    }

    onMounted(() => {
      fetchTasks()
    })

    return {
      tasks,
      input_title,
      addTask,
      deleteTask,
      toggleComplete,
    }
  },
})
</script>

<template>
  <section class="create-task">
    <h3>CREATE A TASK</h3>

    <form id="new-task-form" @submit.prevent="addTask">
      <h4>What are your tasks?</h4>
      <input type="text" name="title" id="title" placeholder="e.g. hire an intern" v-model="input_title" />
      <input type="submit" value="Add Task" />
    </form>
  </section>

  <section class="task-list">
    <h3>TASK LIST</h3>
    <div class="list-wrapper">
      <div v-for="task in tasks" :key="task.id" :class="`task-item`">
        <div class="task-title">
          <input class="toggle" type="button" value="🚩" @click="toggleComplete(task)" />
         <b :class="{ completed: task.completed }">{{ task.title }}</b>
        </div>
        <div class="actions">
          <button class="delete" @click="deleteTask(task)">Delete</button>
        </div>
      </div>
    </div>
  </section>
</template>
