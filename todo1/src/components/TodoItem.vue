<!-- TodoItem.vue -->
<template>
    <div class="task">
      <span :class="{completed: task.completed}">{{ task.text }}</span> 
      <button @click="toggle">{{ task.completed ? "未完成" : "完成" }}</button>
      <button @click="deleteTask">删除</button>
    </div>
  </template>
  
  <script>
  export default {
    name: "TodoItem",
    props: {
      task: Object
    },
    methods: {
      toggle() {
        this.$emit("toggle")
      },
      deleteTask() {
        this.$emit("deleteTask")
      }
    }
  }
  </script>
  
  <style>
  .task {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    border-bottom: 1px solid #ccc;
    transition: background-color 0.3s ease;
  }
  
  .task:hover {
    background-color: #e0e0e0;
  }
  
  .task span {
    flex-grow: 1;
  }
  
  .task span.completed {
    text-decoration: line-through;
    color: #999;
  }
  
  .task button {
    background: none;
    border: none;
    color: #007bff;
    cursor: pointer;
    margin-left: 15px;
  }
  
  .task button:hover {
    color: #0056b3;
  }
  </style>
  