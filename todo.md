### vue和django实现前后端分离的todolist

### 项目目标：
我们依然实现一个简单的 **待办事项应用**，包含以下功能：
1. 添加任务。
2. 删除任务。
3. 标记任务为已完成。
4. 显示任务列表。

### 一、前端实现
### 步骤 1：创建 Vue 3 项目
* npm create vue@3.2
* cd todo1
* npm install
* npm run dev

#### 1.根据提示设置项目名称，如 todolist
#### 2.进入项目文件夹 todolist
#### 3.安装项目依赖执行 npm install。 会安装vite
#### 4.启动项目 npm run dev

 这时你可以在浏览器中访问 `http://localhost:8080` 查看 Vue 项目。

### 步骤 2：实现项目结构
我们可以在 `src` 文件夹中创建一个组件 `TodoItem.vue`，然后在 `App.vue` 中展示任务列表。

```
src/
 ├── assets/
 ├── components/
 │   └── TodoItem.vue
 ├── App.vue
 ├── main.js
```

### 步骤 3：在 `App.vue` 中实现基本功能

#### **App.vue**

在 `App.vue` 中，我们将使用选项式 API 来管理任务列表和添加、删除、标记任务为已完成的功能。

```vue
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
// 引入 TodoItem 子组件
import TodoItem from './components/TodoItem.vue'

export default {
  name: 'App',
  components: {
    TodoItem,
  },
  data() {
    return {
      // 输入框绑定的任务名称
      newTask: '',
      // 存储任务的数组
      tasks: [
        { id: 1, text: '学习 Vue.js', completed: false },
        { id: 2, text: '做待办事项应用', completed: false },
      ]
    }
  },
  methods: {
    // 添加新任务
    addTask() {
      if (this.newTask.trim()) {
        this.tasks.push({
          id: Date.now(),
          text: this.newTask.trim(),
          completed: false,
        })
        this.newTask = '' // 清空输入框
      }
    },
    // 删除任务
    deleteTask(index) {
      this.tasks.splice(index, 1)
    },
    // 切换任务的完成状态
    toggle(index) {
      this.tasks[index].completed = !this.tasks[index].completed
    }
  }
}
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 60px;
}

input {
  padding: 10px;
  margin-bottom: 20px;
  width: 200px;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin: 10px 0;
}

.completed {
  text-decoration: line-through;
}
</style>
```

#### 代码说明：
1. **`newTask` 和 `tasks`**：`newTask` 用于绑定输入框的值，`tasks` 是一个数组，用于存储待办事项的数据。
2. **`addTask` 方法**：当用户按下 Enter 键时，`addTask` 会将新任务添加到 `tasks` 数组中，任务的 `id` 使用当前时间戳来生成，保证唯一性。
3. **`deleteTask` 方法**：删除任务的方法，通过 `splice` 从 `tasks` 数组中移除对应的任务。
4. **`toggle` 方法**：切换任务的完成状态，如果任务已经完成，则将 `completed` 设置为 `false`，否则设置为 `true`。

### 步骤 4：创建 `TodoItem.vue` 子组件

为了让每个任务有独立的视图和行为，我们将任务项提取为 `TodoItem` 组件。这个组件负责显示任务的内容，并提供删除和切换完成状态的功能。

```vue
<template>
    <div class="task">
      <span :class="{completed: task.completed}">{{ task.text }}</span> 
      <button @click="toggle">{{ task.completed ? "未完成" : "完成" }}</button>
      <button @click="deleteTask">删除</button>
    </div>
</template>

<script>
export default {
  name: 'TodoItem',
  props: {
    task: Object, // 接收父组件传来的任务数据
  },
  methods: {
    // 删除任务
    deleteTask() {
      this.$emit('delete') // 发出 delete 事件通知父组件
    },
    // 切换任务的完成状态
    toggle() {
      this.$emit('toggle') // 发出 toggle 事件通知父组件
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
```

### 步骤 5：运行并测试

在完成上述代码后，运行 `npm run serve` 并打开浏览器查看应用效果：

1. **输入框**：你可以输入任务内容并按 Enter 添加任务。
2. **任务列表**：每个任务都有两个按钮，分别用于删除和切换任务的完成状态。
3. **标记已完成**：点击 "已完成" 按钮，任务会变成完成状态，文字会有删除线。
4. **删除任务**：点击 "删除" 按钮，可以将任务从列表中移除。

### 步骤 6：扩展功能

1. **本地存储**：可以通过 `localStorage` 将任务保存到浏览器本地，以便刷新页面后任务数据不会丢失。
2. **任务过滤**：实现过滤功能，按“所有”、“未完成”和“已完成”来筛选任务。
3. **编辑任务**：实现任务编辑功能，可以修改任务的内容。

### 总结

通过这个项目，你应该能够熟练使用 Vue 3 的选项式 API 来进行组件化开发、数据绑定和事件处理。`data`、`methods`、`computed`、`props` 等选项可以帮助你定义组件的状态和行为


### 二、后端实现：

可以通过添加后端来实现任务数据的持久化，使得任务在重新打开页面后不会丢失。你可以使用 **Django 4** 来实现后端，前端通过 **API 请求** 与后端进行交互，来保存和获取任务数据。以下是如何实现前端与 Django 后端集成的步骤：

#### 00.创建虚拟环境：
```bash
python -m venv myenv
```
#### 01.激活虚拟环境：
```bash
myenv\Scripts\activate
```

### 1. 后端部分：使用 Django 创建 API

我们可以使用 Django 的 `rest_framework` 来创建一个简单的 API，使前端能够与后端进行通信。

#### 安装 Django 和 Django REST Framework

首先，你需要安装 Django 和 Django REST Framework（DRF）：
```bash
pip install django==4.2
pip install djangorestframework==3.14
python -m django --version
pip show djangorestframework
```

#### 创建 Django 项目和应用

1. **创建 Django 项目**：
   ```bash
   django-admin startproject todolist
   cd todolist
   ```

2. **创建一个应用**：
   ```bash
   python manage.py startapp tasks
   ```

3. **在 `settings.py` 中添加应用**：
   在 `INSTALLED_APPS` 中添加 `tasks` 和 `rest_framework`：
   ```python
   INSTALLED_APPS = [
       ...
       'rest_framework',
       'tasks',
   ]
   ```

#### 定义 Task 模型

在 `tasks/models.py` 中定义 `Task` 模型：
```python
from django.db import models

class Task(models.Model):
    text = models.CharField(max_length=255)
    completed = models.BooleanField(default=False)

    def __str__(self):
        return self.text
```

#### 创建数据库迁移

生成数据库迁移并应用：
```bash
python manage.py makemigrations
python manage.py migrate
```

#### 创建序列化器

在 `tasks/serializers.py` 中创建一个序列化器，以便将 `Task` 模型数据转换为 JSON 格式：
```python
from rest_framework import serializers
from .models import Task

class TaskSerializer(serializers.ModelSerializer):
    class Meta:
        model = Task
        fields = ['id', 'text', 'completed']
```

#### 创建 API 视图

在 `tasks/views.py` 中创建一个 API 视图，来处理任务的 CRUD 操作：
```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Task
from .serializers import TaskSerializer

class TaskList(APIView):
    def get(self, request):
        tasks = Task.objects.all()
        serializer = TaskSerializer(tasks, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = TaskSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class TaskDetail(APIView):
    def get_object(self, pk):
        try:
            return Task.objects.get(pk=pk)
        except Task.DoesNotExist:
            raise Http404

    def get(self, request, pk):
        task = self.get_object(pk)
        serializer = TaskSerializer(task)
        return Response(serializer.data)

    def put(self, request, pk):
        task = self.get_object(pk)
        serializer = TaskSerializer(task, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        task = self.get_object(pk)
        task.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

#### 配置 URL 路由

在 `tasks/urls.py` 中配置 URL 路由，将请求映射到对应的视图：
```python
from django.urls import path
from . import views

urlpatterns = [
    path('tasks/', views.TaskList.as_view(), name='task_list'),
    path('tasks/<int:pk>/', views.TaskDetail.as_view(), name='task_detail'),
]
```

在 `todolist/urls.py` 中包含 `tasks` 应用的路由：
```python
from django.contrib import admin
from django.urls import path, include  # 导入 include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('tasks.urls')),  # 使用 include() 引入 tasks 应用的 URL
]
```

#### 启动 Django 服务

运行 Django 服务器：
```bash
python manage.py runserver
```

此时，你的 Django 后端 API 就已经可以通过 `http://localhost:8000/api/tasks/` 访问了。

### 2. 前端部分：通过 API 与 Django 后端交互

接下来，我们需要修改 Vue.js 前端，使其能够与 Django 后端进行交互，实现任务的获取、添加、更新和删除。

#### 安装 axios

首先，安装 axios 用于发送 HTTP 请求：
```bash
npm install axios
```

#### 修改 `App.vue` 以连接后端

你需要通过 `axios` 向 Django 后端发送请求。以下是修改后的 `App.vue`：

```vue
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
```

### 3. 测试与调试

1. 确保 Django 后端正在运行（`python manage.py runserver`）。
2. 确保前端 Vue 应用能够访问到后端 API。
3. 在 Vue 应用中测试添加、删除和切换任务的功能，确保数据能够正确存取。

通过这种方式，你就可以将前端与 Django 后端结合起来，实现任务的持久化存储。重新打开应用时，任务数据会从后端加载，任务不会丢失。



### 三、跨域问题解决
因为 **CORS（跨域资源共享）** 策略阻止了前端从 `http://localhost:3000` 向后端 `http://localhost:8000` 发起请求。默认情况下，浏览器会阻止跨域请求，除非后端明确允许它们。

为了修复这个问题，你需要在 **Django 后端** 配置 CORS 头，允许来自 `http://localhost:3000` 的请求。

### 解决方法：使用 `django-cors-headers` 配置 CORS

1. **安装 `django-cors-headers`**：

   你可以通过 `pip` 安装 `django-cors-headers`，它是一个 Django 应用，专门用于处理 CORS 问题。

   ```bash
   pip install django-cors-headers
   ```

2. **配置 `django-cors-headers`**：

   在安装完 `django-cors-headers` 后，按照以下步骤配置它：

   - **添加到 `INSTALLED_APPS`**：
     打开你的 `settings.py` 文件，找到 `INSTALLED_APPS` 配置，并添加 `corsheaders`：

     ```python
     INSTALLED_APPS = [
         # 其他已安装的应用
         'corsheaders',
         # 'django.contrib.admin', 其他应用
     ]
     ```

   - **添加中间件**：
     在 `MIDDLEWARE` 配置中，确保 `CorsMiddleware` 在 `CommonMiddleware` 之前：

     ```python
     MIDDLEWARE = [
         'corsheaders.middleware.CorsMiddleware',  # 确保它在 CommonMiddleware 之前
         'django.middleware.common.CommonMiddleware',
         # 其他中间件
     ]
     ```

   - **允许跨域请求**：
     配置 CORS 来允许来自 `http://localhost:3000` 的请求。你可以选择以下几种方式之一：

     - **允许所有来源**（不安全，适合开发阶段）：
       ```python
       CORS_ALLOW_ALL_ORIGINS = True
       ```

     - **仅允许特定来源**（推荐用于生产环境）：
       ```python
       CORS_ALLOWED_ORIGINS = [
           "http://localhost:3000",  # 允许前端开发服务器的地址
       ]
       ```

   - 如果你需要允许请求携带认证信息（例如 Cookies），可以启用以下配置：

     ```python
     CORS_ALLOW_CREDENTIALS = True
     ```

3. **重新启动 Django 开发服务器**：
   配置完成后，重新启动你的 Django 服务器以使配置生效：

   ```bash
   python manage.py runserver
   ```

### 完整示例配置

在 `settings.py` 中，确保包括以下配置：

```python
INSTALLED_APPS = [
    # 其他已安装的应用
    'corsheaders',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',  # 需要在 CommonMiddleware 前面
    'django.middleware.common.CommonMiddleware',
    # 其他中间件
]

# 允许来自 localhost:3000 的请求
CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
]

# 如果你希望允许带认证信息的请求，可以启用下面的配置
CORS_ALLOW_CREDENTIALS = True
```

### 总结

- 使用 `django-cors-headers` 解决 CORS 问题。
- 配置允许跨域请求的源（比如 `http://localhost:3000`）。
- 重新启动 Django 服务器使配置生效。

这样，你就能够从 `http://localhost:3000` 发起请求到 `http://localhost:8000` 了，解决了 CORS 问题。