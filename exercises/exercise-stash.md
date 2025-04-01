# แบบฝึกหัด: การใช้งาน Git Stash

## สถานการณ์

คุณกำลังพัฒนาแอปพลิเคชัน Todo List และกำลังทำงานบนฟีเจอร์ใหม่สำหรับการจัดลำดับความสำคัญของงาน แต่ในระหว่างนั้น มีบัคเร่งด่วนที่ต้องแก้ไขในฟีเจอร์อื่น คุณต้องการเก็บงานปัจจุบันไว้ชั่วคราวเพื่อไปแก้ไขบัค และกลับมาทำงานเดิมในภายหลัง

### ขั้นตอนการจำลองสถานการณ์

1. สร้างโฟลเดอร์สำหรับโปรเจคและเริ่มต้น Git repository
```bash
mkdir stash-exercise
cd stash-exercise
git init
```

2. สร้างไฟล์ `index.html` ด้วยเนื้อหาเริ่มต้น
```bash
echo '<!DOCTYPE html>
<html>
<head>
  <title>Todo List App</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="container">
    <h1>Todo List</h1>
    <div class="add-todo">
      <input type="text" id="new-todo" placeholder="Add a new task...">
      <button id="add-button">Add</button>
    </div>
    <ul id="todo-list">
      <!-- Todo items will be added here -->
    </ul>
  </div>
  <script src="app.js"></script>
</body>
</html>' > index.html
```

3. สร้างไฟล์ `styles.css`
```bash
echo 'body {
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
  margin: 0;
  padding: 0;
}

.container {
  max-width: 600px;
  margin: 2rem auto;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  padding: 2rem;
}

h1 {
  text-align: center;
  color: #333;
}

.add-todo {
  display: flex;
  margin-bottom: 1rem;
}

#new-todo {
  flex-grow: 1;
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px 0 0 3px;
}

#add-button {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  cursor: pointer;
  border-radius: 0 3px 3px 0;
}

#todo-list {
  list-style-type: none;
  padding: 0;
}

.todo-item {
  display: flex;
  align-items: center;
  padding: 1rem;
  border-bottom: 1px solid #eee;
}

.todo-item:last-child {
  border-bottom: none;
}

.todo-item input[type="checkbox"] {
  margin-right: 1rem;
}

.todo-item.completed span {
  text-decoration: line-through;
  color: #888;
}

.delete-button {
  margin-left: auto;
  background-color: #f44336;
  color: white;
  border: none;
  padding: 0.25rem 0.5rem;
  cursor: pointer;
  border-radius: 3px;
}' > styles.css
```

4. สร้างไฟล์ `app.js`
```bash
echo 'document.addEventListener("DOMContentLoaded", function() {
  const newTodoInput = document.getElementById("new-todo");
  const addButton = document.getElementById("add-button");
  const todoList = document.getElementById("todo-list");
  
  // Load todos from localStorage
  let todos = JSON.parse(localStorage.getItem("todos")) || [];
  
  // Render initial todos
  renderTodos();
  
  // Add new todo
  addButton.addEventListener("click", function() {
    const todoText = newTodoInput.value.trim();
    if (todoText) {
      addTodo(todoText);
      newTodoInput.value = "";
    }
  });
  
  newTodoInput.addEventListener("keypress", function(e) {
    if (e.key === "Enter") {
      addButton.click();
    }
  });
  
  function addTodo(text) {
    const todo = {
      id: Date.now(),
      text,
      completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
  }
  
  function deleteTodo(id) {
    todos = todos.filter(todo => todo.id !== id);
    saveTodos();
    renderTodos();
  }
  
  function toggleTodo(id) {
    todos = todos.map(todo => {
      if (todo.id === id) {
        return { ...todo, completed: !todo.completed };
      }
      return todo;
    });
    saveTodos();
    renderTodos();
  }
  
  function saveTodos() {
    localStorage.setItem("todos", JSON.stringify(todos));
  }
  
  function renderTodos() {
    todoList.innerHTML = "";
    
    todos.forEach(todo => {
      const li = document.createElement("li");
      li.className = `todo-item ${todo.completed ? "completed" : ""}`;
      
      const checkbox = document.createElement("input");
      checkbox.type = "checkbox";
      checkbox.checked = todo.completed;
      checkbox.addEventListener("change", () => toggleTodo(todo.id));
      
      const span = document.createElement("span");
      span.textContent = todo.text;
      
      const deleteButton = document.createElement("button");
      deleteButton.className = "delete-button";
      deleteButton.textContent = "Delete";
      deleteButton.addEventListener("click", () => deleteTodo(todo.id));
      
      li.appendChild(checkbox);
      li.appendChild(span);
      li.appendChild(deleteButton);
      
      todoList.appendChild(li);
    });
  }
});' > app.js
```

5. Commit ไฟล์เริ่มต้น
```bash
git add index.html styles.css app.js
git commit -m "Initial commit with basic Todo List functionality"
```

6. เริ่มทำงานบนฟีเจอร์การจัดลำดับความสำคัญ โดยแก้ไขไฟล์ `index.html`
```bash
echo '<!DOCTYPE html>
<html>
<head>
  <title>Todo List App</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="container">
    <h1>Todo List</h1>
    <div class="add-todo">
      <input type="text" id="new-todo" placeholder="Add a new task...">
      <select id="priority">
        <option value="low">Low</option>
        <option value="medium" selected>Medium</option>
        <option value="high">High</option>
      </select>
      <button id="add-button">Add</button>
    </div>
    <div class="filters">
      <button id="all-filter" class="active">All</button>
      <button id="high-filter">High Priority</button>
      <button id="medium-filter">Medium Priority</button>
      <button id="low-filter">Low Priority</button>
    </div>
    <ul id="todo-list">
      <!-- Todo items will be added here -->
    </ul>
  </div>
  <script src="app.js"></script>
</body>
</html>' > index.html
```

7. แก้ไขไฟล์ `styles.css` เพื่อเพิ่มสไตล์สำหรับฟีเจอร์ใหม่
```bash
echo 'body {
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
  margin: 0;
  padding: 0;
}

.container {
  max-width: 600px;
  margin: 2rem auto;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  padding: 2rem;
}

h1 {
  text-align: center;
  color: #333;
}

.add-todo {
  display: flex;
  margin-bottom: 1rem;
}

#new-todo {
  flex-grow: 1;
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px 0 0 3px;
}

#priority {
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-left: none;
}

#add-button {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  cursor: pointer;
  border-radius: 0 3px 3px 0;
}

.filters {
  display: flex;
  margin-bottom: 1rem;
  gap: 0.5rem;
}

.filters button {
  background-color: #f1f1f1;
  border: none;
  padding: 0.5rem 1rem;
  cursor: pointer;
  border-radius: 3px;
}

.filters button.active {
  background-color: #4caf50;
  color: white;
}

#todo-list {
  list-style-type: none;
  padding: 0;
}

.todo-item {
  display: flex;
  align-items: center;
  padding: 1rem;
  border-bottom: 1px solid #eee;
}

.todo-item:last-child {
  border-bottom: none;
}

.todo-item input[type="checkbox"] {
  margin-right: 1rem;
}

.todo-item.completed span {
  text-decoration: line-through;
  color: #888;
}

.priority-indicator {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  margin-right: 0.5rem;
}

.priority-high {
  background-color: #f44336;
}

.priority-medium {
  background-color: #ff9800;
}

.priority-low {
  background-color: #4caf50;
}

.delete-button {
  margin-left: auto;
  background-color: #f44336;
  color: white;
  border: none;
  padding: 0.25rem 0.5rem;
  cursor: pointer;
  border-radius: 3px;
}' > styles.css
```

8. แก้ไขไฟล์ `app.js` เพื่อเพิ่มฟังก์ชันการจัดลำดับความสำคัญ (ยังไม่เสร็จสมบูรณ์)
```bash
echo 'document.addEventListener("DOMContentLoaded", function() {
  const newTodoInput = document.getElementById("new-todo");
  const prioritySelect = document.getElementById("priority");
  const addButton = document.getElementById("add-button");
  const todoList = document.getElementById("todo-list");
  const allFilter = document.getElementById("all-filter");
  const highFilter = document.getElementById("high-filter");
  const mediumFilter = document.getElementById("medium-filter");
  const lowFilter = document.getElementById("low-filter");
  
  // Load todos from localStorage
  let todos = JSON.parse(localStorage.getItem("todos")) || [];
  let currentFilter = "all";
  
  // Render initial todos
  renderTodos();
  
  // Add new todo
  addButton.addEventListener("click", function() {
    const todoText = newTodoInput.value.trim();
    if (todoText) {
      addTodo(todoText, prioritySelect.value);
      newTodoInput.value = "";
    }
  });
  
  newTodoInput.addEventListener("keypress", function(e) {
    if (e.key === "Enter") {
      addButton.click();
    }
  });
  
  // Set up filter buttons
  allFilter.addEventListener("click", () => setFilter("all"));
  highFilter.addEventListener("click", () => setFilter("high"));
  mediumFilter.addEventListener("click", () => setFilter("medium"));
  lowFilter.addEventListener("click", () => setFilter("low"));
  
  function setFilter(filter) {
    currentFilter = filter;
    
    // Update active button
    [allFilter, highFilter, mediumFilter, lowFilter].forEach(button => {
      button.classList.remove("active");
    });
    
    switch (filter) {
      case "high":
        highFilter.classList.add("active");
        break;
      case "medium":
        mediumFilter.classList.add("active");
        break;
      case "low":
        lowFilter.classList.add("active");
        break;
      default:
        allFilter.classList.add("active");
    }
    
    renderTodos();
  }
  
  function addTodo(text, priority) {
    const todo = {
      id: Date.now(),
      text,
      priority,
      completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
  }
  
  function deleteTodo(id) {
    todos = todos.filter(todo => todo.id !== id);
    saveTodos();
    renderTodos();
  }
  
  function toggleTodo(id) {
    todos = todos.map(todo => {
      if (todo.id === id) {
        return { ...todo, completed: !todo.completed };
      }
      return todo;
    });
    saveTodos();
    renderTodos();
  }
  
  function saveTodos() {
    localStorage.setItem("todos", JSON.stringify(todos));
  }
  
  function renderTodos() {
    todoList.innerHTML = "";
    
    // Filter todos based on current filter
    const filteredTodos = currentFilter === "all" 
      ? todos 
      : todos.filter(todo => todo.priority === currentFilter);
    
    filteredTodos.forEach(todo => {
      const li = document.createElement("li");
      li.className = `todo-item ${todo.completed ? "completed" : ""}`;
      
      const priorityIndicator = document.createElement("div");
      priorityIndicator.className = `priority-indicator priority-${todo.priority || "medium"}`;
      
      const checkbox = document.createElement("input");
      checkbox.type = "checkbox";
      checkbox.checked = todo.completed;
      checkbox.addEventListener("change", () => toggleTodo(todo.id));
      
      const span = document.createElement("span");
      span.textContent = todo.text;
      
      const deleteButton = document.createElement("button");
      deleteButton.className = "delete-button";
      deleteButton.textContent = "Delete";
      deleteButton.addEventListener("click", () => deleteTodo(todo.id));
      
      li.appendChild(priorityIndicator);
      li.appendChild(checkbox);
      li.appendChild(span);
      li.appendChild(deleteButton);
      
      todoList.appendChild(li);
    });
  }
  
  // TODO: Add drag and drop functionality for reordering todos
  // TODO: Add ability to edit priority of existing todos
});' > app.js
```

## คำถาม

1. ในขณะที่คุณกำลังทำงานบนฟีเจอร์การจัดลำดับความสำคัญ มีรายงานบัคว่าปุ่ม Delete ไม่ทำงานในบางกรณี คุณต้องการเก็บงานปัจจุบันไว้ชั่วคราวเพื่อไปแก้ไขบัค คุณจะใช้คำสั่ง Git อะไร?

2. หลังจากเก็บงานไว้ชั่วคราวแล้ว คุณต้องการกลับไปที่ commit ล่าสุดเพื่อแก้ไขบัค คุณจะใช้คำสั่ง Git อะไร?

3. คุณแก้ไขบัคในไฟล์ `app.js` เสร็จแล้ว และต้องการ commit การแก้ไข คุณจะใช้คำสั่ง Git อะไร?

4. หลังจาก commit การแก้ไขบัคแล้ว คุณต้องการกลับไปทำงานบนฟีเจอร์การจัดลำดับความสำคัญต่อ คุณจะใช้คำสั่ง Git อะไรเพื่อนำการเปลี่ยนแปลงที่เก็บไว้ชั่วคราวกลับมา?

5. ถ้าเกิด conflict เมื่อนำการเปลี่ยนแปลงกลับมา คุณจะแก้ไขอย่างไร?

6. คุณมีการเปลี่ยนแปลงหลายชุดที่เก็บไว้ใน stash และต้องการดูรายการทั้งหมด คุณจะใช้คำสั่ง Git อะไร?

## เฉลย

### 1. การเก็บงานปัจจุบันไว้ชั่วคราว

```bash
git stash save "WIP: Priority feature implementation"
```

หรือใช้คำสั่งย่อ:

```bash
git stash
```

### 2. การกลับไปที่ commit ล่าสุด

หลังจาก stash แล้ว คุณจะอยู่ที่ commit ล่าสุดโดยอัตโนมัติ แต่ถ้าต้องการให้แน่ใจ:

```bash
git checkout main
```

หรือถ้าคุณอยู่ใน branch อื่น:

```bash
git checkout <branch-name>
```

### 3. การ commit การแก้ไขบัค

หลังจากแก้ไขไฟล์ `app.js`:

```bash
git add app.js
git commit -m "Fix delete button bug"
```

### 4. การนำการเปลี่ยนแปลงที่เก็บไว้ชั่วคราวกลับมา

```bash
git stash pop
```

หรือถ้าต้องการเก็บการเปลี่ยนแปลงไว้ใน stash:

```bash
git stash apply
```

ถ้ามีหลาย stash และต้องการเลือกเฉพาะ:

```bash
git stash list
git stash apply stash@{n}  # n คือลำดับของ stash
```

### 5. การแก้ไข conflict เมื่อนำการเปลี่ยนแปลงกลับมา

เมื่อเกิด conflict:

1. เปิดไฟล์ที่มี conflict และแก้ไข
2. ทำการ add ไฟล์ที่แก้ไขแล้ว
3. ถ้าใช้ `git stash pop` และเกิด conflict, stash จะยังคงอยู่ในรายการ stash จนกว่าคุณจะลบมันด้วย `git stash drop`

```bash
# หลังจากแก้ไข conflict
git add <conflicted-files>
# ถ้าต้องการลบ stash ที่ใช้แล้ว
git stash drop
```

### 6. การดูรายการ stash ทั้งหมด

```bash
git stash list
```

ผลลัพธ์จะแสดงรายการ stash ทั้งหมด เช่น:
```
stash@{0}: WIP: Priority feature implementation
stash@{1}: WIP on main: abc1234 Initial commit with basic Todo List functionality
```

ถ้าต้องการดูรายละเอียดของ stash เฉพาะ:

```bash
git stash show stash@{0}
```

ถ้าต้องการดูการเปลี่ยนแปลงในรูปแบบ diff:

```bash
git stash show -p stash@{0}
```

## ข้อแนะนำเพิ่มเติม

1. **ใช้ข้อความอธิบายที่ชัดเจน**: เมื่อใช้ `git stash save` ให้ใส่ข้อความอธิบายที่ชัดเจนเพื่อให้ง่ายต่อการค้นหาในภายหลัง

2. **ใช้ `git stash branch`**: ถ้าคุณคาดว่าอาจเกิด conflict เมื่อนำการเปลี่ยนแปลงกลับมา ให้ใช้ `git stash branch <new-branch-name>` เพื่อสร้าง branch ใหม่จาก stash

```bash
git stash branch feature/priority-from-stash
```

3. **ลบ stash ที่ไม่ใช้แล้ว**: เพื่อป้องกันความสับสน ควรลบ stash ที่ไม่ได้ใช้แล้ว

```bash
git stash drop stash@{0}
# หรือลบทั้งหมด
git stash clear
```

4. **ใช้ stash เฉพาะบางไฟล์**: ถ้าต้องการ stash เฉพาะบางไฟล์

```bash
git stash push -m "Stash specific files" file1.js file2.js
```

## แบบฝึกหัดเพิ่มเติม

ลองทำตามขั้นตอนการจำลองสถานการณ์และทดลองใช้ Git Stash:

1. สร้างการเปลี่ยนแปลงในไฟล์ต่างๆ และใช้ `git stash` เพื่อเก็บไว้ชั่วคราว
2. สร้างการเปลี่ยนแปลงอื่นและ commit
3. ใช้ `git stash pop` เพื่อนำการเปลี่ยนแปลงที่เก็บไว้กลับมา
4. ลองสร้าง stash หลายชุดและใช้ `git stash list` เพื่อดูรายการ
5. ใช้ `git stash show -p stash@{n}` เพื่อดูรายละเอียดของ stash
6. ใช้ `git stash branch` เพื่อสร้าง branch ใหม่จาก stash
