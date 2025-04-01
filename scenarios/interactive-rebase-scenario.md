# สถานการณ์จำลอง: การใช้ Interactive Rebase เพื่อจัดการประวัติ Commit

## สถานการณ์

สมมติว่าคุณกำลังพัฒนาฟีเจอร์ใหม่สำหรับแอปพลิเคชันและได้ทำการ commit หลายครั้งระหว่างการพัฒนา แต่ก่อนที่จะ merge เข้า main branch คุณต้องการจัดการประวัติ commit ให้สะอาดและเป็นระเบียบ โดย:

1. รวม (squash) commit ย่อยๆ ที่เกี่ยวข้องกันเข้าด้วยกัน
2. แก้ไขข้อความ commit ที่ไม่ชัดเจน
3. เปลี่ยนลำดับของ commit บางรายการ
4. ลบ commit ที่ไม่จำเป็น
5. แก้ไขเนื้อหาของ commit เก่า

Interactive rebase เป็นเครื่องมือที่เหมาะสมสำหรับงานนี้

## การจำลองสถานการณ์

### 1. สร้างโปรเจคจำลอง

```bash
mkdir rebase-demo
cd rebase-demo
git init
```

### 2. สร้างไฟล์เริ่มต้นและ commit แรก

```bash
# สร้างไฟล์ app.js
echo 'class TaskManager {
  constructor() {
    this.tasks = [];
  }
  
  addTask(task) {
    this.tasks.push(task);
  }
  
  listTasks() {
    return this.tasks;
  }
}

module.exports = TaskManager;' > app.js

# สร้างไฟล์ index.js
echo 'const TaskManager = require("./app");

const taskManager = new TaskManager();

// Add some initial tasks
taskManager.addTask({ id: 1, title: "Learn Git", completed: false });
taskManager.addTask({ id: 2, title: "Learn JavaScript", completed: false });

console.log("Tasks:", taskManager.listTasks());' > index.js

# commit ไฟล์เริ่มต้น
git add app.js index.js
git commit -m "Initial commit with basic task manager"
```

### 3. สร้าง branch สำหรับฟีเจอร์ใหม่

```bash
git checkout -b feature/task-management
```

### 4. ทำการพัฒนาและ commit หลายครั้ง

```bash
# commit 1: เพิ่มฟังก์ชัน removeTask
echo 'class TaskManager {
  constructor() {
    this.tasks = [];
  }
  
  addTask(task) {
    this.tasks.push(task);
  }
  
  removeTask(taskId) {
    this.tasks = this.tasks.filter(task => task.id !== taskId);
  }
  
  listTasks() {
    return this.tasks;
  }
}

module.exports = TaskManager;' > app.js
git add app.js
git commit -m "Add removeTask function"

# commit 2: แก้ไขบัคในฟังก์ชัน removeTask
echo 'class TaskManager {
  constructor() {
    this.tasks = [];
  }
  
  addTask(task) {
    this.tasks.push(task);
  }
  
  removeTask(taskId) {
    const index = this.tasks.findIndex(task => task.id === taskId);
    if (index !== -1) {
      this.tasks.splice(index, 1);
    }
  }
  
  listTasks() {
    return this.tasks;
  }
}

module.exports = TaskManager;' > app.js
git add app.js
git commit -m "Fix bug in removeTask function"

# commit 3: เพิ่มฟังก์ชัน updateTask
echo 'class TaskManager {
  constructor() {
    this.tasks = [];
  }
  
  addTask(task) {
    this.tasks.push(task);
  }
  
  removeTask(taskId) {
    const index = this.tasks.findIndex(task => task.id === taskId);
    if (index !== -1) {
      this.tasks.splice(index, 1);
    }
  }
  
  updateTask(taskId, updates) {
    const task = this.tasks.find(task => task.id === taskId);
    if (task) {
      Object.assign(task, updates);
    }
  }
  
  listTasks() {
    return this.tasks;
  }
}

module.exports = TaskManager;' > app.js
git add app.js
git commit -m "Add updateTask function"

# commit 4: เพิ่ม console.log สำหรับการดีบัก (commit ที่ไม่จำเป็น)
echo 'class TaskManager {
  constructor() {
    this.tasks = [];
    console.log("TaskManager initialized");
  }
  
  addTask(task) {
    this.tasks.push(task);
    console.log("Task added:", task);
  }
  
  removeTask(taskId) {
    const index = this.tasks.findIndex(task => task.id === taskId);
    if (index !== -1) {
      this.tasks.splice(index, 1);
      console.log("Task removed:", taskId);
    }
  }
  
  updateTask(taskId, updates) {
    const task = this.tasks.find(task => task.id === taskId);
    if (task) {
      Object.assign(task, updates);
      console.log("Task updated:", task);
    }
  }
  
  listTasks() {
    console.log("Listing tasks");
    return this.tasks;
  }
}

module.exports = TaskManager;' > app.js
git add app.js
git commit -m "Add debug logs"

# commit 5: ปรับปรุงไฟล์ index.js เพื่อใช้ฟังก์ชันใหม่
echo 'const TaskManager = require("./app");

const taskManager = new TaskManager();

// Add some initial tasks
taskManager.addTask({ id: 1, title: "Learn Git", completed: false });
taskManager.addTask({ id: 2, title: "Learn JavaScript", completed: false });

// Update a task
taskManager.updateTask(1, { completed: true });

// Remove a task
taskManager.removeTask(2);

// Add a new task
taskManager.addTask({ id: 3, title: "Learn Node.js", completed: false });

console.log("Tasks:", taskManager.listTasks());' > index.js
git add index.js
git commit -m "Update index.js to use new functions"

# commit 6: เพิ่มฟังก์ชัน filterTasks
echo 'class TaskManager {
  constructor() {
    this.tasks = [];
    console.log("TaskManager initialized");
  }
  
  addTask(task) {
    this.tasks.push(task);
    console.log("Task added:", task);
  }
  
  removeTask(taskId) {
    const index = this.tasks.findIndex(task => task.id === taskId);
    if (index !== -1) {
      this.tasks.splice(index, 1);
      console.log("Task removed:", taskId);
    }
  }
  
  updateTask(taskId, updates) {
    const task = this.tasks.find(task => task.id === taskId);
    if (task) {
      Object.assign(task, updates);
      console.log("Task updated:", task);
    }
  }
  
  filterTasks(predicate) {
    return this.tasks.filter(predicate);
  }
  
  listTasks() {
    console.log("Listing tasks");
    return this.tasks;
  }
}

module.exports = TaskManager;' > app.js
git add app.js
git commit -m "Add filterTasks function"

# commit 7: แก้ไขชื่อไฟล์ที่ import ผิดใน index.js
echo 'const TaskManager = require("./app");

const taskManager = new TaskManager();

// Add some initial tasks
taskManager.addTask({ id: 1, title: "Learn Git", completed: false });
taskManager.addTask({ id: 2, title: "Learn JavaScript", completed: false });

// Update a task
taskManager.updateTask(1, { completed: true });

// Remove a task
taskManager.removeTask(2);

// Add a new task
taskManager.addTask({ id: 3, title: "Learn Node.js", completed: false });

// Filter completed tasks
const completedTasks = taskManager.filterTasks(task => task.completed);
console.log("Completed tasks:", completedTasks);

console.log("All tasks:", taskManager.listTasks());' > index.js
git add index.js
git commit -m "Fix import in index.js and use filterTasks"
```

## การใช้ Interactive Rebase เพื่อจัดการประวัติ Commit

ตอนนี้เรามี commit ทั้งหมด 7 รายการบน feature branch ซึ่งบางรายการควรรวมเข้าด้วยกัน บางรายการควรลบ และบางรายการควรแก้ไขข้อความ เราจะใช้ interactive rebase เพื่อจัดการประวัติ commit เหล่านี้

### 1. เริ่มต้น Interactive Rebase

```bash
# เริ่ม interactive rebase ย้อนกลับไป 7 commit
git rebase -i HEAD~7
```

Git จะเปิดตัวแก้ไขข้อความพร้อมรายการ commit และคำสั่งที่สามารถใช้ได้:

```
pick 1a2b3c4 Add removeTask function
pick 2b3c4d5 Fix bug in removeTask function
pick 3c4d5e6 Add updateTask function
pick 4d5e6f7 Add debug logs
pick 5e6f7g8 Update index.js to use new functions
pick 6f7g8h9 Add filterTasks function
pick 7g8h9i0 Fix import in index.js and use filterTasks

# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

### 2. แก้ไขรายการ Commit

เราจะแก้ไขรายการ commit ดังนี้:

1. รวม commit "Fix bug in removeTask function" เข้ากับ "Add removeTask function"
2. ลบ commit "Add debug logs" เพราะไม่จำเป็น
3. แก้ไขข้อความ commit "Update index.js to use new functions" ให้ชัดเจนขึ้น
4. รวม commit "Fix import in index.js and use filterTasks" เข้ากับ "Add filterTasks function"

แก้ไขไฟล์ให้เป็นดังนี้:

```
pick 1a2b3c4 Add removeTask function
fixup 2b3c4d5 Fix bug in removeTask function
pick 3c4d5e6 Add updateTask function
drop 4d5e6f7 Add debug logs
reword 5e6f7g8 Update index.js to use new functions
pick 6f7g8h9 Add filterTasks function
fixup 7g8h9i0 Fix import in index.js and use filterTasks
```

บันทึกและปิดไฟล์

### 3. แก้ไขข้อความ Commit

Git จะหยุดที่ commit ที่เราเลือก "reword" และให้เราแก้ไขข้อความ:

```
Update index.js to demonstrate task management functionality
```

บันทึกและปิดไฟล์

### 4. ลบ Debug Logs จาก Commit

หลังจาก rebase เสร็จสิ้น เราจะลบ debug logs ที่ไม่จำเป็นออกจากโค้ด:

```bash
# แก้ไขไฟล์ app.js เพื่อลบ debug logs
echo 'class TaskManager {
  constructor() {
    this.tasks = [];
  }
  
  addTask(task) {
    this.tasks.push(task);
  }
  
  removeTask(taskId) {
    const index = this.tasks.findIndex(task => task.id === taskId);
    if (index !== -1) {
      this.tasks.splice(index, 1);
    }
  }
  
  updateTask(taskId, updates) {
    const task = this.tasks.find(task => task.id === taskId);
    if (task) {
      Object.assign(task, updates);
    }
  }
  
  filterTasks(predicate) {
    return this.tasks.filter(predicate);
  }
  
  listTasks() {
    return this.tasks;
  }
}

module.exports = TaskManager;' > app.js

# commit การเปลี่ยนแปลง
git add app.js
git commit -m "Remove debug logs"
```

### 5. ตรวจสอบประวัติ Commit หลังจาก Rebase

```bash
git log --oneline
```

ผลลัพธ์ควรจะเป็นประมาณนี้:

```
abcd123 Remove debug logs
efgh456 Add filterTasks function
ijkl789 Update index.js to demonstrate task management functionality
mnop012 Add updateTask function
qrst345 Add removeTask function
uvwx678 Initial commit with basic task manager
```

## สถานการณ์เพิ่มเติม: การแยก Commit

บางครั้งคุณอาจต้องการแยก commit ออกเป็นหลาย commit ย่อย เราจะจำลองสถานการณ์นี้:

### 1. สร้าง Commit ที่มีการเปลี่ยนแปลงหลายอย่าง

```bash
# แก้ไขไฟล์ app.js เพิ่มฟังก์ชันหลายอย่างพร้อมกัน
echo 'class TaskManager {
  constructor() {
    this.tasks = [];
    this.nextId = 1;
  }
  
  addTask(task) {
    if (!task.id) {
      task.id = this.nextId++;
    }
    this.tasks.push(task);
  }
  
  removeTask(taskId) {
    const index = this.tasks.findIndex(task => task.id === taskId);
    if (index !== -1) {
      this.tasks.splice(index, 1);
    }
  }
  
  updateTask(taskId, updates) {
    const task = this.tasks.find(task => task.id === taskId);
    if (task) {
      Object.assign(task, updates);
    }
  }
  
  filterTasks(predicate) {
    return this.tasks.filter(predicate);
  }
  
  listTasks() {
    return this.tasks;
  }
  
  getTaskById(taskId) {
    return this.tasks.find(task => task.id === taskId);
  }
  
  markTaskComplete(taskId) {
    const task = this.getTaskById(taskId);
    if (task) {
      task.completed = true;
    }
  }
  
  markTaskIncomplete(taskId) {
    const task = this.getTaskById(taskId);
    if (task) {
      task.completed = false;
    }
  }
  
  getSummary() {
    const total = this.tasks.length;
    const completed = this.tasks.filter(task => task.completed).length;
    const incomplete = total - completed;
    
    return {
      total,
      completed,
      incomplete
    };
  }
}

module.exports = TaskManager;' > app.js

# commit การเปลี่ยนแปลงทั้งหมดในครั้งเดียว
git add app.js
git commit -m "Add multiple task management features"
```

### 2. ใช้ Interactive Rebase เพื่อแยก Commit

```bash
# เริ่ม interactive rebase
git rebase -i HEAD~1
```

แก้ไขไฟล์ให้เป็นดังนี้:

```
edit abcd123 Add multiple task management features
```

บันทึกและปิดไฟล์

### 3. แยก Commit

```bash
# ยกเลิก commit แต่เก็บการเปลี่ยนแปลงไว้
git reset HEAD^

# commit แยกเป็นส่วนๆ
# 1. commit ส่วนของ auto-increment ID
git add -p  # เลือกเฉพาะส่วนที่เกี่ยวกับ nextId
git commit -m "Add auto-increment ID for new tasks"

# 2. commit ส่วนของฟังก์ชัน getTaskById
git add -p  # เลือกเฉพาะส่วนที่เกี่ยวกับ getTaskById
git commit -m "Add getTaskById function"

# 3. commit ส่วนของฟังก์ชันจัดการสถานะ task
git add -p  # เลือกส่วนที่เกี่ยวกับ markTaskComplete และ markTaskIncomplete
git commit -m "Add functions to mark tasks as complete or incomplete"

# 4. commit ส่วนของฟังก์ชัน getSummary
git add -p  # เลือกส่วนที่เหลือ
git commit -m "Add getSummary function for task statistics"

# ดำเนินการ rebase ต่อ
git rebase --continue
```

### 4. ตรวจสอบประวัติ Commit หลังจากแยก Commit

```bash
git log --oneline
```

ผลลัพธ์ควรจะเป็นประมาณนี้:

```
abcd123 Add getSummary function for task statistics
efgh456 Add functions to mark tasks as complete or incomplete
ijkl789 Add getTaskById function
mnop012 Add auto-increment ID for new tasks
qrst345 Remove debug logs
uvwx678 Add filterTasks function
yzab901 Update index.js to demonstrate task management functionality
bcde234 Add updateTask function
fghi567 Add removeTask function
jklm890 Initial commit with basic task manager
```

## สถานการณ์เพิ่มเติม: การเปลี่ยนลำดับ Commit

บางครั้งคุณอาจต้องการเปลี่ยนลำดับของ commit เพื่อให้ประวัติมีความเป็นเหตุเป็นผลมากขึ้น:

### 1. ใช้ Interactive Rebase เพื่อเปลี่ยนลำดับ Commit

```bash
# เริ่ม interactive rebase ย้อนกลับไป 4 commit
git rebase -i HEAD~4
```

สมมติว่าเราต้องการย้าย "Add getTaskById function" ไปก่อน "Add auto-increment ID for new tasks" เพราะฟังก์ชัน getTaskById ถูกใช้โดยฟังก์ชันอื่นๆ:

```
pick ijkl789 Add getTaskById function
pick mnop012 Add auto-increment ID for new tasks
pick efgh456 Add functions to mark tasks as complete or incomplete
pick abcd123 Add getSummary function for task statistics
```

บันทึกและปิดไฟล์

### 2. ตรวจสอบประวัติ Commit หลังจากเปลี่ยนลำดับ

```bash
git log --oneline
```

## สรุป

Interactive rebase เป็นเครื่องมือที่ทรงพลังสำหรับการจัดการประวัติ commit ก่อนที่จะ push ไปยัง remote repository หรือ merge เข้า main branch ช่วยให้คุณสามารถ:

1. รวม commit ที่เกี่ยวข้องกันเข้าด้วยกัน
2. แก้ไขข้อความ commit ให้ชัดเจนและมีความหมาย
3. ลบ commit ที่ไม่จำเป็น เช่น commit ที่เกี่ยวกับการดีบัก
4. แยก commit ที่มีการเปลี่ยนแปลงหลายอย่างออกเป็น commit ย่อยๆ
5. เปลี่ยนลำดับของ commit เพื่อให้ประวัติมีความเป็นเหตุเป็นผลมากขึ้น

ข้อควรระวัง:
- ไม่ควรใช้ interactive rebase กับ commit ที่ได้ push ไปยัง remote repository แล้ว เว้นแต่คุณทำงานคนเดียวหรือได้แจ้งทีมแล้ว
- ควรทำ backup branch ก่อนทำ interactive rebase เพื่อให้สามารถกู้คืนได้หากเกิดปัญหา
- ควรทดสอบโค้ดหลังจากทำ rebase เสร็จเพื่อให้แน่ใจว่ายังทำงานได้ถูกต้อง

การมีประวัติ commit ที่สะอาดและเป็นระเบียบช่วยให้ทีมสามารถเข้าใจการเปลี่ยนแปลงได้ง่ายขึ้น และทำให้การ review โค้ดมีประสิทธิภาพมากขึ้น
