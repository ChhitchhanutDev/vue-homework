<template>
<div class="app">
  
<!-- TODO 1: Input + v-model -->
<input v-model="newTask" placeholder="Add task">
<button @click="addTask">Add</button>

<!-- TODO 2: v-for task list -->
<ul>
  <li
    v-for="task in filteredTasks"
    :key="task.id"
    :class="{ done: task.done }"
  >

    <input
      type="checkbox"
      v-model="task.done"
    />

    {{ task.name }}

    <button @click="deleteTask(task.id)">
      Delete
    </button>

  </li>
</ul>

<!-- TODO 3: v-if empty state -->
<p v-if="tasks.length === 0">
  No tasks yet!
</p>

<p v-else>
  Total: {{ filteredTasks.length }}
</p>
<!-- TODO 4: Filter buttons -->
<button @click="filter='all'">All</button>
<button @click="filter='active'">Active</button>
<button @click="filter='done'">Done</button>

</div>
</template>

<script>
export default {
  data() {
    return {
      newTask: "",
      tasks: [],
      filter: "all"
    }
  },

  computed: {
    filteredTasks() {

      if (this.filter === "active") {
        return this.tasks.filter(
          task => !task.done
        )
      }

      if (this.filter === "done") {
        return this.tasks.filter(
          task => task.done
        )
      }

      return this.tasks
    }
  },

  methods: {

    addTask() {
      if (!this.newTask) return

      this.tasks.push({
        id: Date.now(),
        name: this.newTask,
        done: false
      })

      this.newTask = ""
    },

    deleteTask(id) {
      this.tasks = this.tasks.filter(
        task => task.id !== id
      )
    }
  }
}
</script>

<style scoped>
.done {
  text-decoration: line-through;
}
</style>
