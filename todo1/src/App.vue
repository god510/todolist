<template>
  <div id="app" class="app-container">
    <div class="header">
      <h1>代办事项</h1>
    </div>
    <div class="main">
      <div class="taskinput">
        <input type="text" v-model="newTask" @keyup.enter="addTask" placeholder="请输入代办事项">
        <button @click="addTask" :disabled="!newTask.trim()">添加</button>
      </div>

      <div class="taskList">
        <TodoItem v-for="task in tasks" 
          :key="task.id" 
          :task="task" 
          @toggle="toggle(task.id)"
          @deleteTask="deleteTask(task.id)">
        </TodoItem>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import TodoItem from "./components/TodoItem.vue"

export default {
  components: {
    TodoItem,
  },
  data() {
    return {
      newTask: "",
      tasks: []
    }
  },
  methods: {
    // 获取任务列表
    getTasks() {
      axios.get('http://localhost:8000/api/tasks/')
        .then(response => {
          this.tasks = response.data
        })
        .catch(error => {
          console.error("There was an error fetching tasks:", error)
        })
    },
    // 添加任务
    addTask() {
      if (this.newTask.trim()) {
        axios.post('http://localhost:8000/api/tasks/', {
          text: this.newTask,
          completed: false
        })
        .then(response => {
          this.tasks.push(response.data)
          this.newTask = ""
        })
        .catch(error => {
          console.error("There was an error adding the task:", error)
        })
      }
    },

    // 切换任务完成状态
    toggle(id) {
      const task = this.tasks.find(task => task.id === id)
      if (task.isToggling) return // 如果正在进行请求，直接返回
      
      task.isToggling = true  // 标记正在处理请求
      const previousState = task.completed
      task.completed = !task.completed  // 临时改变状态
      
      axios.put(`http://localhost:8000/api/tasks/${id}/`, task)
        .then(response => {
          task.isToggling = false
          console.log("Task toggled successfully:", response.data)
        })
        .catch(error => {
          task.completed = previousState  // 恢复原状态
          task.isToggling = false
          console.error("There was an error toggling the task:", error)
        })
    },

    // 删除任务
    deleteTask(id) {
      axios.delete(`http://localhost:8000/api/tasks/${id}/`)
        .then(() => {
          this.tasks = this.tasks.filter(task => task.id !== id)
        })
        .catch(error => {
          console.error("There was an error deleting the task:", error)
        })
    }
  },
  mounted() {
    this.getTasks()  // 页面加载时获取任务
  }
}
</script>
