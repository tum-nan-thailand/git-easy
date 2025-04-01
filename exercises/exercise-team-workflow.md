# แบบฝึกหัด: สถานการณ์การทำงานเป็นทีม

## สถานการณ์

คุณเป็นส่วนหนึ่งของทีมพัฒนาเว็บแอปพลิเคชัน โดยทีมของคุณใช้ Git ในการจัดการโค้ด ในแบบฝึกหัดนี้ คุณจะได้เรียนรู้วิธีการจัดการกับสถานการณ์ต่างๆ ที่มักพบในการทำงานเป็นทีม

### ขั้นตอนการจำลองสถานการณ์

1. สร้างโฟลเดอร์สำหรับโปรเจคและเริ่มต้น Git repository
```bash
mkdir team-workflow-exercise
cd team-workflow-exercise
git init
```

2. สร้างไฟล์ `README.md` เพื่อเป็นเอกสารเริ่มต้น
```bash
echo '# Team Project

This is a collaborative project for our team.

## Getting Started

1. Clone the repository
2. Install dependencies
3. Run the development server

## Features

- User authentication
- Dashboard
- Reports' > README.md
```

3. สร้างไฟล์ `index.html` เริ่มต้น
```bash
echo '<!DOCTYPE html>
<html>
<head>
  <title>Team Project</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Welcome to Our Project</h1>
    <nav>
      <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/about">About</a></li>
        <li><a href="/contact">Contact</a></li>
      </ul>
    </nav>
  </header>
  <main>
    <section>
      <h2>About Our Project</h2>
      <p>This is a collaborative project for our team.</p>
    </section>
  </main>
  <footer>
    <p>&copy; 2023 Team Project</p>
  </footer>
  <script src="app.js"></script>
</body>
</html>' > index.html
```

4. สร้างไฟล์ `styles.css` เริ่มต้น
```bash
echo 'body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

header {
  background-color: #333;
  color: white;
  padding: 1rem;
}

nav ul {
  display: flex;
  list-style: none;
  padding: 0;
}

nav ul li {
  margin-right: 1rem;
}

nav ul li a {
  color: white;
  text-decoration: none;
}

main {
  padding: 2rem;
}

footer {
  background-color: #333;
  color: white;
  padding: 1rem;
  text-align: center;
}' > styles.css
```

5. สร้างไฟล์ `app.js` เริ่มต้น
```bash
echo 'document.addEventListener("DOMContentLoaded", function() {
  console.log("Application loaded");
});' > app.js
```

6. Commit ไฟล์เริ่มต้น
```bash
git add README.md index.html styles.css app.js
git commit -m "Initial project setup"
```

7. สร้าง branch `main` และ push ไปยัง remote (จำลอง)
```bash
git branch -M main
# ในสถานการณ์จริง คุณจะใช้คำสั่ง git remote add และ git push
# แต่ในแบบฝึกหัดนี้ เราจะจำลองว่าได้ push ไปแล้ว
```

8. สร้าง branch `feature/user-authentication` และเริ่มทำงานบนฟีเจอร์นี้
```bash
git checkout -b feature/user-authentication
```

9. สร้างไฟล์ `login.html`
```bash
echo '<!DOCTYPE html>
<html>
<head>
  <title>Login - Team Project</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Login</h1>
    <nav>
      <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/about">About</a></li>
        <li><a href="/contact">Contact</a></li>
      </ul>
    </nav>
  </header>
  <main>
    <section class="login-form">
      <h2>Login to Your Account</h2>
      <form id="login-form">
        <div class="form-group">
          <label for="username">Username:</label>
          <input type="text" id="username" name="username" required>
        </div>
        <div class="form-group">
          <label for="password">Password:</label>
          <input type="password" id="password" name="password" required>
        </div>
        <button type="submit">Login</button>
      </form>
    </section>
  </main>
  <footer>
    <p>&copy; 2023 Team Project</p>
  </footer>
  <script src="app.js"></script>
  <script src="auth.js"></script>
</body>
</html>' > login.html
```

10. สร้างไฟล์ `auth.js`
```bash
echo 'document.addEventListener("DOMContentLoaded", function() {
  const loginForm = document.getElementById("login-form");
  
  if (loginForm) {
    loginForm.addEventListener("submit", function(e) {
      e.preventDefault();
      
      const username = document.getElementById("username").value;
      const password = document.getElementById("password").value;
      
      // Simulate authentication
      if (username === "admin" && password === "password") {
        alert("Login successful!");
        // Redirect to dashboard in a real app
      } else {
        alert("Invalid credentials. Please try again.");
      }
    });
  }
});' > auth.js
```

11. แก้ไขไฟล์ `styles.css` เพื่อเพิ่มสไตล์สำหรับฟอร์มล็อกอิน
```bash
echo 'body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

header {
  background-color: #333;
  color: white;
  padding: 1rem;
}

nav ul {
  display: flex;
  list-style: none;
  padding: 0;
}

nav ul li {
  margin-right: 1rem;
}

nav ul li a {
  color: white;
  text-decoration: none;
}

main {
  padding: 2rem;
}

.login-form {
  max-width: 400px;
  margin: 0 auto;
}

.form-group {
  margin-bottom: 1rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
}

.form-group input {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
}

button {
  background-color: #333;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  cursor: pointer;
  border-radius: 3px;
}

footer {
  background-color: #333;
  color: white;
  padding: 1rem;
  text-align: center;
}' > styles.css
```

12. Commit การเปลี่ยนแปลง
```bash
git add login.html auth.js styles.css
git commit -m "Add login page and authentication"
```

13. ในขณะเดียวกัน สมมติว่าเพื่อนร่วมทีมของคุณได้แก้ไขไฟล์ `styles.css` ใน branch `main` และ push ไปแล้ว
```bash
git checkout main
```

14. แก้ไขไฟล์ `styles.css` เพื่อจำลองการเปลี่ยนแปลงของเพื่อนร่วมทีม
```bash
echo 'body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  color: #333;
  line-height: 1.6;
}

header {
  background-color: #4a90e2;
  color: white;
  padding: 1rem;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

nav ul {
  display: flex;
  list-style: none;
  padding: 0;
}

nav ul li {
  margin-right: 1rem;
}

nav ul li a {
  color: white;
  text-decoration: none;
  font-weight: bold;
}

nav ul li a:hover {
  text-decoration: underline;
}

main {
  padding: 2rem;
  max-width: 1200px;
  margin: 0 auto;
}

section {
  margin-bottom: 2rem;
}

h1, h2, h3 {
  color: #4a90e2;
}

footer {
  background-color: #4a90e2;
  color: white;
  padding: 1rem;
  text-align: center;
}' > styles.css
```

15. Commit การเปลี่ยนแปลง
```bash
git add styles.css
git commit -m "Update styles with new color scheme and improved typography"
```

## คำถาม

### สถานการณ์ 1: การ Merge Feature Branch

1. คุณต้องการรวมการเปลี่ยนแปลงจาก branch `feature/user-authentication` เข้ากับ branch `main` แต่คุณคาดว่าจะเกิด conflict ในไฟล์ `styles.css` คุณจะใช้คำสั่ง Git อะไรเพื่อ merge และแก้ไข conflict?

2. หลังจากแก้ไข conflict แล้ว คุณพบว่ามีข้อผิดพลาดในโค้ดและต้องการยกเลิกการ merge คุณจะใช้คำสั่ง Git อะไร?

### สถานการณ์ 2: การ Rebase Feature Branch

3. แทนที่จะใช้ merge คุณต้องการใช้ rebase เพื่อให้ประวัติ commit เป็นเส้นตรง คุณจะใช้คำสั่ง Git อะไร?

4. ระหว่างการ rebase เกิด conflict ในไฟล์ `styles.css` คุณจะแก้ไขอย่างไร และใช้คำสั่ง Git อะไรเพื่อดำเนินการ rebase ต่อ?

### สถานการณ์ 3: การแก้ไข Commit ที่ Push แล้ว

5. หลังจาก push branch `feature/user-authentication` ไปยัง remote repository แล้ว คุณพบว่ามีข้อมูลที่ละเอียดอ่อน (เช่น รหัสผ่านตายตัว) ในไฟล์ `auth.js` คุณจะใช้คำสั่ง Git อะไรเพื่อแก้ไข commit ล่าสุดและ push การเปลี่ยนแปลงไปยัง remote?

### สถานการณ์ 4: การจัดการกับ Hotfix

6. ในขณะที่คุณกำลังทำงานบน branch `feature/user-authentication` มีบัคเร่งด่วนที่ต้องแก้ไขใน production (branch `main`) คุณจะใช้คำสั่ง Git อะไรเพื่อเก็บงานปัจจุบันไว้ชั่วคราว สร้าง hotfix branch จาก `main` แก้ไขบัค และ merge กลับไปยัง `main`?

### สถานการณ์ 5: การ Reset เมื่อมีปัญหา

7. คุณได้ทำการ commit หลายครั้งใน branch `feature/user-authentication` แต่พบว่าแนวทางที่ใช้ไม่ถูกต้อง คุณต้องการย้อนกลับไปยัง commit ก่อนหน้า 3 commits แต่ยังคงเก็บการเปลี่ยนแปลงไว้เพื่อเริ่มต้นใหม่ คุณจะใช้คำสั่ง Git อะไร?

## เฉลย

### สถานการณ์ 1: การ Merge Feature Branch

#### 1. การ merge และแก้ไข conflict

```bash
git checkout main
git merge feature/user-authentication
```

เมื่อเกิด conflict ในไฟล์ `styles.css`:

1. เปิดไฟล์และแก้ไข conflict โดยรวมทั้งสองส่วนอย่างเหมาะสม
2. ทำการ add ไฟล์ที่แก้ไขแล้ว
3. ทำการ commit

```bash
git add styles.css
git commit
```

#### 2. การยกเลิกการ merge

```bash
git merge --abort
```

ถ้าได้ทำการ commit การ merge ไปแล้ว:

```bash
git reset --hard HEAD~1
```

### สถานการณ์ 2: การ Rebase Feature Branch

#### 3. การ rebase feature branch

```bash
git checkout feature/user-authentication
git rebase main
```

#### 4. การแก้ไข conflict ระหว่าง rebase

1. เปิดไฟล์ `styles.css` และแก้ไข conflict
2. ทำการ add ไฟล์ที่แก้ไขแล้ว
3. ดำเนินการ rebase ต่อ

```bash
git add styles.css
git rebase --continue
```

ถ้าต้องการยกเลิกการ rebase:

```bash
git rebase --abort
```

### สถานการณ์ 3: การแก้ไข Commit ที่ Push แล้ว

#### 5. การแก้ไข commit ล่าสุดและ force push

1. แก้ไขไฟล์ `auth.js` เพื่อลบข้อมูลที่ละเอียดอ่อน
2. ทำการ add ไฟล์และแก้ไข commit ล่าสุด
3. ทำการ force push

```bash
git add auth.js
git commit --amend
git push --force-with-lease origin feature/user-authentication
```

**หมายเหตุ**: การ force push อาจส่งผลกระทบต่อผู้อื่นที่ใช้ branch เดียวกัน ควรใช้ด้วยความระมัดระวังและแจ้งทีมก่อน

### สถานการณ์ 4: การจัดการกับ Hotfix

#### 6. การสร้าง hotfix และ merge กลับไปยัง main

```bash
# เก็บงานปัจจุบันไว้ชั่วคราว
git stash save "WIP on authentication feature"

# สร้าง hotfix branch จาก main
git checkout main
git checkout -b hotfix/critical-bug

# แก้ไขบัค
# ... แก้ไขไฟล์ที่เกี่ยวข้อง ...
git add <fixed-files>
git commit -m "Fix critical bug"

# merge hotfix กลับไปยัง main
git checkout main
git merge hotfix/critical-bug

# กลับไปทำงานเดิม
git checkout feature/user-authentication
git stash pop
```

### สถานการณ์ 5: การ Reset เมื่อมีปัญหา

#### 7. การย้อนกลับไปยัง commit ก่อนหน้าแต่เก็บการเปลี่ยนแปลงไว้

```bash
git reset HEAD~3
```

หรือถ้าต้องการระบุ commit เฉพาะ:

```bash
git reset <commit-hash>
```

หลังจากนั้น คุณสามารถแก้ไขไฟล์ตามต้องการและ commit ใหม่:

```bash
git add .
git commit -m "Restart authentication implementation with better approach"
```

## ข้อแนะนำเพิ่มเติมสำหรับการทำงานเป็นทีม

1. **ใช้ Branch Strategy ที่เหมาะสม**: เช่น Git Flow หรือ GitHub Flow เพื่อให้การทำงานเป็นระบบ

2. **Pull/Rebase บ่อยๆ**: เพื่อลดโอกาสเกิด conflict ขนาดใหญ่

```bash
git pull --rebase origin main
```

3. **ใช้ Pull Request/Merge Request**: เพื่อให้มีการ review code ก่อน merge

4. **เขียน Commit Message ที่มีความหมาย**: ใช้รูปแบบที่ชัดเจน เช่น

```
feat: Add login functionality
fix: Correct header alignment in mobile view
docs: Update README with setup instructions
```

5. **ใช้ .gitignore อย่างเหมาะสม**: เพื่อไม่ให้ไฟล์ที่ไม่เกี่ยวข้องเข้าไปใน repository

6. **ระวังการใช้ Force Push**: ใช้เฉพาะกับ branch ส่วนตัวหรือแจ้งทีมก่อน และใช้ `--force-with-lease` แทน `--force`

7. **ใช้ Git Hooks**: เพื่อตรวจสอบคุณภาพโค้ดก่อน commit หรือ push

## แบบฝึกหัดเพิ่มเติม

ลองทำตามขั้นตอนการจำลองสถานการณ์และทดลองแก้ไขปัญหาต่างๆ ที่อาจเกิดขึ้นในการทำงานเป็นทีม:

1. จำลองสถานการณ์ที่มีการ merge conflict และแก้ไข
2. ทดลองใช้ rebase แทน merge และเปรียบเทียบผลลัพธ์
3. จำลองสถานการณ์ที่ต้องสร้าง hotfix ในขณะที่กำลังทำงานบน feature branch
4. ทดลองใช้ interactive rebase เพื่อจัดการกับ commits หลายอัน
5. จำลองสถานการณ์ที่ต้องแก้ไข commit ที่ push ไปแล้ว
