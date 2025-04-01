# สถานการณ์จำลอง: การใช้ Git Bisect เพื่อค้นหา Bug

## สถานการณ์

สมมติว่าคุณเป็นนักพัฒนาในทีมที่กำลังพัฒนาเว็บแอปพลิเคชัน ทีมของคุณพบว่ามีบัคในฟีเจอร์การคำนวณราคาสินค้าในตะกร้า แต่ไม่ทราบว่าบัคนี้เกิดขึ้นตั้งแต่เมื่อไหร่ คุณรู้เพียงว่าเมื่อ 2 สัปดาห์ที่แล้วฟีเจอร์นี้ยังทำงานได้ดี แต่ตอนนี้มีปัญหา คุณต้องการค้นหาว่า commit ใดที่ทำให้เกิดบัคนี้

## การจำลองสถานการณ์

### 1. สร้างโปรเจคจำลอง

```bash
mkdir bisect-demo
cd bisect-demo
git init
```

### 2. สร้างไฟล์เริ่มต้นที่ทำงานได้ถูกต้อง

```bash
# สร้างไฟล์ calculator.js
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => {
    return total + (item.price * item.quantity);
  }, 0);
}

function formatCurrency(amount) {
  return "$" + amount.toFixed(2);
}

module.exports = {
  calculateTotal,
  formatCurrency
};' > calculator.js

# สร้างไฟล์ทดสอบ
echo 'const calculator = require("./calculator");

// ข้อมูลทดสอบ
const cart = [
  { name: "Product 1", price: 10, quantity: 2 },
  { name: "Product 2", price: 15, quantity: 1 },
  { name: "Product 3", price: 20, quantity: 3 }
];

// คำนวณราคารวม
const total = calculator.calculateTotal(cart);
console.log("Total:", calculator.formatCurrency(total));

// ตรวจสอบว่าผลลัพธ์ถูกต้อง
const expectedTotal = 95; // (10*2) + (15*1) + (20*3)
if (total === expectedTotal) {
  console.log("Test passed!");
  process.exit(0);
} else {
  console.log(`Test failed! Expected: $${expectedTotal}, Got: $${total}`);
  process.exit(1);
}' > test.js

# commit ไฟล์เริ่มต้น
git add calculator.js test.js
git commit -m "Initial commit with working calculator"

# ทดสอบว่าโค้ดทำงานได้ถูกต้อง
node test.js
```

### 3. สร้าง commit หลายๆ ครั้งที่ไม่เกี่ยวข้องกับบัค

```bash
# commit 1: เพิ่มฟังก์ชันคำนวณภาษี
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => {
    return total + (item.price * item.quantity);
  }, 0);
}

function formatCurrency(amount) {
  return "$" + amount.toFixed(2);
}

function calculateTax(amount, taxRate = 0.07) {
  return amount * taxRate;
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateTax
};' > calculator.js
git add calculator.js
git commit -m "Add tax calculation function"

# commit 2: เพิ่มฟังก์ชันคำนวณส่วนลด
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => {
    return total + (item.price * item.quantity);
  }, 0);
}

function formatCurrency(amount) {
  return "$" + amount.toFixed(2);
}

function calculateTax(amount, taxRate = 0.07) {
  return amount * taxRate;
}

function calculateDiscount(amount, discountRate = 0.1) {
  return amount * discountRate;
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateTax,
  calculateDiscount
};' > calculator.js
git add calculator.js
git commit -m "Add discount calculation function"

# commit 3: ปรับปรุงฟังก์ชัน formatCurrency
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => {
    return total + (item.price * item.quantity);
  }, 0);
}

function formatCurrency(amount) {
  return "$" + amount.toFixed(2).replace(/\d(?=(\d{3})+\.)/g, "$&,");
}

function calculateTax(amount, taxRate = 0.07) {
  return amount * taxRate;
}

function calculateDiscount(amount, discountRate = 0.1) {
  return amount * discountRate;
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateTax,
  calculateDiscount
};' > calculator.js
git add calculator.js
git commit -m "Improve currency formatting with thousand separators"
```

### 4. สร้าง commit ที่มีบัค

```bash
# commit 4: แก้ไขฟังก์ชัน calculateTotal แต่มีบัค
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => {
    // บัค: ลืมคูณด้วย quantity
    return total + item.price;
  }, 0);
}

function formatCurrency(amount) {
  return "$" + amount.toFixed(2).replace(/\d(?=(\d{3})+\.)/g, "$&,");
}

function calculateTax(amount, taxRate = 0.07) {
  return amount * taxRate;
}

function calculateDiscount(amount, discountRate = 0.1) {
  return amount * discountRate;
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateTax,
  calculateDiscount
};' > calculator.js
git add calculator.js
git commit -m "Refactor calculateTotal function for better performance"
```

### 5. สร้าง commit เพิ่มเติมที่ไม่เกี่ยวข้องกับบัค

```bash
# commit 5: เพิ่มฟังก์ชันคำนวณค่าจัดส่ง
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => {
    // บัคยังคงอยู่
    return total + item.price;
  }, 0);
}

function formatCurrency(amount) {
  return "$" + amount.toFixed(2).replace(/\d(?=(\d{3})+\.)/g, "$&,");
}

function calculateTax(amount, taxRate = 0.07) {
  return amount * taxRate;
}

function calculateDiscount(amount, discountRate = 0.1) {
  return amount * discountRate;
}

function calculateShipping(total, items) {
  if (total > 100) return 0;
  return 5 + (items.length * 0.5);
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateTax,
  calculateDiscount,
  calculateShipping
};' > calculator.js
git add calculator.js
git commit -m "Add shipping cost calculation"

# commit 6: เพิ่มฟังก์ชันตรวจสอบคูปอง
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => {
    // บัคยังคงอยู่
    return total + item.price;
  }, 0);
}

function formatCurrency(amount) {
  return "$" + amount.toFixed(2).replace(/\d(?=(\d{3})+\.)/g, "$&,");
}

function calculateTax(amount, taxRate = 0.07) {
  return amount * taxRate;
}

function calculateDiscount(amount, discountRate = 0.1) {
  return amount * discountRate;
}

function calculateShipping(total, items) {
  if (total > 100) return 0;
  return 5 + (items.length * 0.5);
}

function validateCoupon(code, validCodes = ["SAVE10", "FREESHIP"]) {
  return validCodes.includes(code);
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateTax,
  calculateDiscount,
  calculateShipping,
  validateCoupon
};' > calculator.js
git add calculator.js
git commit -m "Add coupon validation function"
```

### 6. ทดสอบและพบบัค

```bash
# ทดสอบโค้ดและพบว่ามีบัค
node test.js
# ผลลัพธ์: Test failed! Expected: $95, Got: $45
```

## การใช้ Git Bisect เพื่อค้นหาบัค

### 1. เริ่มต้นกระบวนการ bisect

```bash
git bisect start
```

### 2. ระบุ commit ปัจจุบันที่มีบัค

```bash
git bisect bad
```

### 3. ระบุ commit เก่าที่ยังทำงานได้ดี

```bash
# สมมติว่า commit แรกคือ commit ที่ยังทำงานได้ดี
git bisect good HEAD~6
```

### 4. Git จะ checkout commit ตรงกลางให้ทดสอบ

```bash
# ทดสอบโค้ด
node test.js

# ถ้าผ่าน (ไม่มีบัค):
git bisect good

# ถ้าไม่ผ่าน (มีบัค):
git bisect bad
```

### 5. ทำซ้ำจนกว่า Git จะบอกว่าพบ commit ที่ทำให้เกิดบัค

Git จะแสดงข้อความประมาณนี้:
```
b1a534c is the first bad commit
commit b1a534c...
Author: ...
Date: ...

    Refactor calculateTotal function for better performance
```

### 6. เมื่อเสร็จสิ้น ให้จบกระบวนการ bisect

```bash
git bisect reset
```

## การแก้ไขบัค

เมื่อพบ commit ที่ทำให้เกิดบัคแล้ว คุณสามารถแก้ไขได้:

```bash
# แก้ไขไฟล์ calculator.js
echo 'function calculateTotal(items) {
  return items.reduce((total, item) => {
    // แก้ไขบัค: เพิ่มการคูณด้วย quantity กลับมา
    return total + (item.price * item.quantity);
  }, 0);
}

function formatCurrency(amount) {
  return "$" + amount.toFixed(2).replace(/\d(?=(\d{3})+\.)/g, "$&,");
}

function calculateTax(amount, taxRate = 0.07) {
  return amount * taxRate;
}

function calculateDiscount(amount, discountRate = 0.1) {
  return amount * discountRate;
}

function calculateShipping(total, items) {
  if (total > 100) return 0;
  return 5 + (items.length * 0.5);
}

function validateCoupon(code, validCodes = ["SAVE10", "FREESHIP"]) {
  return validCodes.includes(code);
}

module.exports = {
  calculateTotal,
  formatCurrency,
  calculateTax,
  calculateDiscount,
  calculateShipping,
  validateCoupon
};' > calculator.js

# ทดสอบว่าแก้ไขบัคสำเร็จหรือไม่
node test.js

# commit การแก้ไข
git add calculator.js
git commit -m "Fix bug in calculateTotal: restore quantity multiplication"
```

## การใช้ Git Bisect แบบอัตโนมัติ

หากคุณมีสคริปต์ทดสอบที่สามารถตรวจสอบบัคได้ คุณสามารถให้ Git ทำงานอัตโนมัติได้:

```bash
git bisect start
git bisect bad  # commit ปัจจุบันมีบัค
git bisect good HEAD~6  # commit เก่าที่ยังทำงานได้ดี
git bisect run node test.js  # รันสคริปต์ทดสอบอัตโนมัติ
```

Git จะทำการทดสอบทุก commit ที่เลือกโดยอัตโนมัติและแสดงผลลัพธ์เมื่อพบ commit ที่ทำให้เกิดบัค

## สรุป

Git Bisect เป็นเครื่องมือที่ทรงพลังในการค้นหาบัคที่แอบแฝงอยู่ในประวัติ commit โดยใช้วิธีการค้นหาแบบ binary search ซึ่งช่วยให้คุณสามารถค้นหาบัคได้อย่างรวดเร็ว แม้ในโปรเจคที่มี commit จำนวนมาก

ในสถานการณ์จริง คุณอาจต้องทำงานกับโค้ดที่ซับซ้อนกว่านี้ แต่หลักการใช้งาน Git Bisect ยังคงเหมือนเดิม:
1. เริ่มต้นกระบวนการ bisect
2. ระบุ commit ที่มีบัคและไม่มีบัค
3. ทดสอบ commit ที่ Git เลือกให้
4. ทำซ้ำจนกว่าจะพบต้นตอของปัญหา

การใช้ Git Bisect ร่วมกับการทดสอบอัตโนมัติจะช่วยให้กระบวนการค้นหาบัคมีประสิทธิภาพมากยิ่งขึ้น
