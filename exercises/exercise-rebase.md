# แบบฝึกหัด: การใช้งาน Git Rebase

## สถานการณ์

คุณกำลังทำงานในโปรเจค API Service ร่วมกับทีม คุณได้สร้าง branch `feature/user-authentication` เพื่อพัฒนาระบบยืนยันตัวตนของผู้ใช้ ในระหว่างที่คุณกำลังพัฒนา มีการอัพเดท branch `main` หลายครั้ง และคุณต้องการอัพเดท branch ของคุณให้ทันสมัย นอกจากนี้ คุณยังต้องการจัดการกับ commits ของคุณให้สะอาดก่อนที่จะ merge กลับเข้า `main`

### ขั้นตอนการจำลองสถานการณ์

1. สร้างโฟลเดอร์สำหรับโปรเจคและเริ่มต้น Git repository
```bash
mkdir rebase-exercise
cd rebase-exercise
git init
```

2. สร้างไฟล์ `app.js` ด้วยเนื้อหาเริ่มต้น
```bash
echo 'const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});' > app.js
```

3. Commit ไฟล์เริ่มต้น
```bash
git add app.js
git commit -m "Initial commit with Express server"
```

4. สร้าง branch `feature/user-authentication` และเพิ่มไฟล์ใหม่
```bash
git checkout -b feature/user-authentication
```

5. สร้างไฟล์ `auth.js`
```bash
echo 'function authenticate(username, password) {
  // TODO: Implement authentication logic
  return username === "admin" && password === "password";
}

module.exports = { authenticate };' > auth.js
```

6. Commit ไฟล์ใหม่
```bash
git add auth.js
git commit -m "Add basic authentication function"
```

7. แก้ไขไฟล์ `auth.js` เพื่อเพิ่มฟังก์ชันใหม่
```bash
echo 'function authenticate(username, password) {
  // TODO: Implement authentication logic
  return username === "admin" && password === "password";
}

function generateToken(user) {
  // TODO: Implement token generation
  return "dummy-token";
}

module.exports = { authenticate, generateToken };' > auth.js
```

8. Commit การเปลี่ยนแปลง
```bash
git add auth.js
git commit -m "Add token generation function"
```

9. แก้ไขไฟล์ `app.js` เพื่อเพิ่ม route สำหรับ authentication
```bash
echo 'const express = require("express");
const { authenticate, generateToken } = require("./auth");
const app = express();
const port = 3000;

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.post("/login", (req, res) => {
  const { username, password } = req.body;
  if (authenticate(username, password)) {
    const token = generateToken({ username });
    res.json({ token });
  } else {
    res.status(401).json({ error: "Invalid credentials" });
  }
});

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});' > app.js
```

10. Commit การเปลี่ยนแปลง
```bash
git add app.js
git commit -m "Add login endpoint"
```

11. กลับไปที่ branch main และเพิ่มการเปลี่ยนแปลงใหม่
```bash
git checkout main
```

12. แก้ไขไฟล์ `app.js` เพื่อเพิ่ม route ใหม่
```bash
echo 'const express = require("express");
const app = express();
const port = 3000;

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.get("/health", (req, res) => {
  res.json({ status: "ok" });
});

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});' > app.js
```

13. Commit การเปลี่ยนแปลง
```bash
git add app.js
git commit -m "Add health check endpoint"
```

14. เพิ่มไฟล์ `config.js`
```bash
echo 'module.exports = {
  port: process.env.PORT || 3000,
  environment: process.env.NODE_ENV || "development"
};' > config.js
```

15. แก้ไขไฟล์ `app.js` เพื่อใช้ config
```bash
echo 'const express = require("express");
const config = require("./config");
const app = express();
const port = config.port;

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.get("/health", (req, res) => {
  res.json({ status: "ok", environment: config.environment });
});

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});' > app.js
```

16. Commit การเปลี่ยนแปลง
```bash
git add config.js app.js
git commit -m "Add configuration file and use it in app"
```

## คำถาม

1. คุณจะใช้คำสั่ง Git อะไรเพื่อ rebase branch `feature/user-authentication` กับ branch `main`?

2. เมื่อเกิด conflict ในไฟล์ `app.js` ระหว่างการ rebase คุณจะแก้ไขอย่างไร?

3. หลังจากแก้ไข conflict แล้ว คุณจะใช้คำสั่ง Git อะไรเพื่อดำเนินการ rebase ต่อ?

4. คุณจะใช้คำสั่ง Git อะไรเพื่อรวม (squash) commits ทั้งหมดใน branch `feature/user-authentication` เป็น commit เดียว?

5. ถ้าคุณต้องการยกเลิกการ rebase ระหว่างดำเนินการ คุณจะใช้คำสั่ง Git อะไร?

6. หลังจาก rebase เสร็จสิ้น คุณจะใช้คำสั่ง Git อะไรเพื่อ push branch `feature/user-authentication` ไปยัง remote repository?

## เฉลย

### 1. คำสั่งสำหรับ rebase branch กับ main

```bash
git checkout feature/user-authentication
git rebase main
```

### 2. การแก้ไข conflict ในไฟล์ app.js

เมื่อเกิด conflict คุณจะเห็นเนื้อหาประมาณนี้:

```javascript
<<<<<<< HEAD
const express = require("express");
const config = require("./config");
const app = express();
const port = config.port;

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.get("/health", (req, res) => {
  res.json({ status: "ok", environment: config.environment });
});
=======
const express = require("express");
const { authenticate, generateToken } = require("./auth");
const app = express();
const port = 3000;

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.post("/login", (req, res) => {
  const { username, password } = req.body;
  if (authenticate(username, password)) {
    const token = generateToken({ username });
    res.json({ token });
  } else {
    res.status(401).json({ error: "Invalid credentials" });
  }
});
>>>>>>> Add login endpoint
```

แก้ไขไฟล์ให้รวมทั้งสองส่วน:

```javascript
const express = require("express");
const config = require("./config");
const { authenticate, generateToken } = require("./auth");
const app = express();
const port = config.port;

app.use(express.json());

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.get("/health", (req, res) => {
  res.json({ status: "ok", environment: config.environment });
});

app.post("/login", (req, res) => {
  const { username, password } = req.body;
  if (authenticate(username, password)) {
    const token = generateToken({ username });
    res.json({ token });
  } else {
    res.status(401).json({ error: "Invalid credentials" });
  }
});
```

### 3. คำสั่งสำหรับดำเนินการ rebase ต่อ

หลังจากแก้ไข conflict และบันทึกไฟล์แล้ว:

```bash
git add app.js
git rebase --continue
```

### 4. คำสั่งสำหรับรวม commits เป็น commit เดียว

```bash
git rebase -i main
```

จากนั้นในตัวแก้ไขที่เปิดขึ้น เปลี่ยน `pick` เป็น `squash` หรือ `s` สำหรับทุก commit ยกเว้น commit แรก:

```
pick abc1234 Add basic authentication function
squash def5678 Add token generation function
squash ghi9012 Add login endpoint
```

บันทึกและปิดตัวแก้ไข จากนั้นในตัวแก้ไขที่เปิดขึ้นใหม่ ให้แก้ไขข้อความ commit ตามต้องการ

### 5. คำสั่งสำหรับยกเลิกการ rebase

```bash
git rebase --abort
```

### 6. คำสั่งสำหรับ push branch หลัง rebase

เนื่องจากการ rebase เปลี่ยนแปลงประวัติ commit คุณจะต้องใช้ force push:

```bash
git push origin feature/user-authentication --force
```

หรือใช้ตัวย่อ:

```bash
git push -f origin feature/user-authentication
```

**หมายเหตุ**: ควรใช้ `--force-with-lease` แทน `--force` เพื่อป้องกันการเขียนทับการเปลี่ยนแปลงของผู้อื่น:

```bash
git push --force-with-lease origin feature/user-authentication
```

## ข้อแนะนำเพิ่มเติม

1. **ทำ rebase บ่อยๆ**: เพื่อลดโอกาสเกิด conflict ขนาดใหญ่ ควรทำ rebase กับ main บ่อยๆ

2. **ระวังการ force push**: การ force push อาจส่งผลกระทบต่อผู้อื่นที่ใช้ branch เดียวกัน ควรใช้เฉพาะกับ branch ส่วนตัวหรือแจ้งทีมก่อน

3. **ใช้ interactive rebase อย่างระมัดระวัง**: การแก้ไขประวัติ commit อาจทำให้เกิดปัญหาได้ ควรทำความเข้าใจผลลัพธ์ก่อนใช้

4. **ทดสอบหลัง rebase**: หลังจาก rebase ควรทดสอบว่าโค้ดยังทำงานได้ถูกต้องหรือไม่

## แบบฝึกหัดเพิ่มเติม

ลองทำตามขั้นตอนการจำลองสถานการณ์และทำการ rebase ด้วยตัวเอง จากนั้นลองใช้ interactive rebase เพื่อ:

1. แก้ไขข้อความ commit
2. รวม commits บางส่วนเข้าด้วยกัน
3. ลบ commit ที่ไม่ต้องการ
4. สลับลำดับ commits
