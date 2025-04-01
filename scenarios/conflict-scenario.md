# สถานการณ์จำลอง: Git Conflict

## สถานการณ์ที่ 1: Conflict จากการ Merge

### สถานการณ์
สมมติว่าคุณและเพื่อนร่วมทีมกำลังทำงานบนโปรเจคเดียวกัน โดยคุณทำงานบน branch `feature-a` และเพื่อนของคุณทำงานบน branch `feature-b` ทั้งสองคนแก้ไขไฟล์เดียวกันในบรรทัดเดียวกัน เมื่อมีการ merge จะเกิด conflict

### ขั้นตอนการจำลองสถานการณ์

1. สร้าง repository ใหม่
```bash
mkdir git-conflict-demo
cd git-conflict-demo
git init
```

2. สร้างไฟล์ `index.js` ด้วยเนื้อหาเริ่มต้น
```bash
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => total + item.price, 0);
}

module.exports = {
  calculateTotal
};' > index.js
```

3. Commit ไฟล์เริ่มต้น
```bash
git add index.js
git commit -m "Initial commit with calculateTotal function"
```

4. สร้าง branch `feature-a` และแก้ไขไฟล์
```bash
git checkout -b feature-a
# แก้ไขไฟล์ index.js
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => total + item.price * item.quantity, 0);
}

module.exports = {
  calculateTotal
};' > index.js
git add index.js
git commit -m "Update calculateTotal to include quantity"
```

5. กลับไปที่ branch main และสร้าง branch `feature-b`
```bash
git checkout main
git checkout -b feature-b
# แก้ไขไฟล์ index.js แต่ด้วยวิธีที่แตกต่าง
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => total + (item.price - item.discount), 0);
}

module.exports = {
  calculateTotal
};' > index.js
git add index.js
git commit -m "Update calculateTotal to include discount"
```

6. พยายาม merge `feature-a` เข้ากับ `feature-b`
```bash
git checkout feature-b
git merge feature-a
```

### ผลลัพธ์
คุณจะเห็นข้อความแจ้งเตือน conflict ประมาณนี้:
```
Auto-merging index.js
CONFLICT (content): Merge conflict in index.js
Automatic merge failed; fix conflicts and then commit the result.
```

### วิธีแก้ไข Conflict

1. เปิดไฟล์ที่มี conflict ด้วย editor
```bash
# เปิดไฟล์ index.js ด้วย editor ที่คุณใช้
```

2. คุณจะเห็นเนื้อหาประมาณนี้:
```javascript
<<<<<<< HEAD
function calculateTotal(items) {
  return items.reduce((total, item) => total + (item.price - item.discount), 0);
}
=======
function calculateTotal(items) {
  return items.reduce((total, item) => total + item.price * item.quantity, 0);
}
>>>>>>> feature-a
```

3. แก้ไขไฟล์ให้รวมทั้งสองฟีเจอร์:
```javascript
function calculateTotal(items) {
  return items.reduce((total, item) => total + (item.price - item.discount) * item.quantity, 0);
}
```

4. บันทึกไฟล์และทำการ commit
```bash
git add index.js
git commit -m "Merge feature-a into feature-b with both quantity and discount"
```

## สถานการณ์ที่ 2: Conflict จากการ Rebase

### สถานการณ์
คุณกำลังทำงานบน branch `feature-c` ที่แยกมาจาก `main` เมื่อนานมาแล้ว ในระหว่างนั้น `main` มีการเปลี่ยนแปลงมากมาย คุณต้องการ rebase branch ของคุณเพื่อให้ทันสมัย แต่เกิด conflict

### ขั้นตอนการจำลองสถานการณ์

1. กลับไปที่ branch main และสร้างไฟล์ใหม่
```bash
git checkout main
echo 'function formatCurrency(amount) {
  return "$" + amount.toFixed(2);
}

module.exports = {
  calculateTotal,
  formatCurrency
};' > index.js
git add index.js
git commit -m "Add formatCurrency function"
```

2. สร้าง branch `feature-c` และแก้ไขไฟล์
```bash
git checkout -b feature-c
echo 'function formatCurrency(amount) {
  return "$" + amount.toFixed(2);
}

function calculateDiscount(item) {
  return item.price * (item.discountPercent / 100);
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateDiscount
};' > index.js
git add index.js
git commit -m "Add calculateDiscount function"
```

3. กลับไปที่ branch main และแก้ไขไฟล์เดียวกัน
```bash
git checkout main
echo 'function formatCurrency(amount) {
  return "$" + amount.toFixed(2);
}

function calculateTax(item, taxRate) {
  return item.price * (taxRate / 100);
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateTax
};' > index.js
git add index.js
git commit -m "Add calculateTax function"
```

4. พยายาม rebase branch `feature-c` กับ `main`
```bash
git checkout feature-c
git rebase main
```

### ผลลัพธ์
คุณจะเห็นข้อความแจ้งเตือน conflict ประมาณนี้:
```
Auto-merging index.js
CONFLICT (content): Merge conflict in index.js
error: could not apply <commit-hash>... Add calculateDiscount function
```

### วิธีแก้ไข Conflict ด้วย Rebase

1. แก้ไขไฟล์ที่มี conflict
```bash
# เปิดไฟล์ index.js ด้วย editor ที่คุณใช้
```

2. แก้ไขให้รวมทั้งสองฟังก์ชัน:
```javascript
function formatCurrency(amount) {
  return "$" + amount.toFixed(2);
}

function calculateTax(item, taxRate) {
  return item.price * (taxRate / 100);
}

function calculateDiscount(item) {
  return item.price * (item.discountPercent / 100);
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateTax,
  calculateDiscount
};
```

3. บันทึกไฟล์และทำการ continue rebase
```bash
git add index.js
git rebase --continue
```

## สรุป

เมื่อเกิด conflict ในการใช้งาน Git คุณจะต้อง:
1. ตรวจสอบไฟล์ที่มี conflict
2. แก้ไขไฟล์ให้รวมการเปลี่ยนแปลงทั้งสองส่วนอย่างเหมาะสม
3. ทำการ add ไฟล์ที่แก้ไขแล้ว
4. ทำการ commit หรือ rebase --continue ตามสถานการณ์

ในหัวข้อถัดไป เราจะเรียนรู้เพิ่มเติมเกี่ยวกับการใช้งาน [Git Rebase](../guides/git-rebase-guide.md) อย่างละเอียด
