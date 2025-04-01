# แบบฝึกหัด: การแก้ไข Git Conflict

## สถานการณ์

คุณกำลังทำงานในโปรเจค Web Application ร่วมกับทีม โดยคุณได้รับมอบหมายให้พัฒนาฟีเจอร์การคำนวณราคาสินค้า ในขณะที่เพื่อนร่วมทีมของคุณกำลังพัฒนาฟีเจอร์การคำนวณส่วนลด ทั้งคุณและเพื่อนร่วมทีมต่างก็แก้ไขไฟล์ `calculator.js` เดียวกัน

### ขั้นตอนการจำลองสถานการณ์

1. สร้างโฟลเดอร์สำหรับโปรเจคและเริ่มต้น Git repository
```bash
mkdir conflict-exercise
cd conflict-exercise
git init
```

2. สร้างไฟล์ `calculator.js` ด้วยเนื้อหาเริ่มต้น
```bash
echo 'class PriceCalculator {
  constructor() {
    this.taxRate = 7; // 7% tax rate
  }

  calculatePrice(product) {
    return product.basePrice;
  }

  calculateTax(product) {
    return this.calculatePrice(product) * (this.taxRate / 100);
  }

  calculateTotal(product) {
    return this.calculatePrice(product) + this.calculateTax(product);
  }
}

module.exports = PriceCalculator;' > calculator.js
```

3. Commit ไฟล์เริ่มต้น
```bash
git add calculator.js
git commit -m "Initial implementation of PriceCalculator"
```

4. สร้าง branch `feature/price-calculation` และแก้ไขไฟล์
```bash
git checkout -b feature/price-calculation
```

5. แก้ไขไฟล์ `calculator.js` เพื่อเพิ่มการคำนวณราคาตามจำนวนสินค้า
```bash
# แก้ไขไฟล์ calculator.js
# เปลี่ยนเมธอด calculatePrice เป็น:
#
# calculatePrice(product) {
#   return product.basePrice * product.quantity;
# }
```

6. Commit การเปลี่ยนแปลง
```bash
git add calculator.js
git commit -m "Update price calculation to include quantity"
```

7. กลับไปที่ branch main และสร้าง branch `feature/discount`
```bash
git checkout main
git checkout -b feature/discount
```

8. แก้ไขไฟล์ `calculator.js` เพื่อเพิ่มการคำนวณส่วนลด
```bash
# แก้ไขไฟล์ calculator.js
# เปลี่ยนเมธอด calculatePrice เป็น:
#
# calculatePrice(product) {
#   return product.basePrice - product.discount;
# }
```

9. Commit การเปลี่ยนแปลง
```bash
git add calculator.js
git commit -m "Update price calculation to include discount"
```

## คำถาม

1. สมมติว่าคุณต้องการรวม branch `feature/price-calculation` เข้ากับ branch `feature/discount` คุณจะใช้คำสั่ง Git อะไร?

2. เมื่อเกิด conflict ในไฟล์ `calculator.js` คุณจะเห็นเนื้อหาประมาณใด?

3. คุณจะแก้ไข conflict อย่างไรเพื่อให้สามารถคำนวณทั้งจำนวนสินค้าและส่วนลดได้?

4. หลังจากแก้ไข conflict แล้ว คุณจะใช้คำสั่ง Git อะไรเพื่อทำการ commit การแก้ไข?

5. ถ้าคุณต้องการยกเลิกการ merge และเริ่มใหม่ คุณจะใช้คำสั่ง Git อะไร?

## เฉลย

### 1. คำสั่งสำหรับการรวม branch

```bash
git checkout feature/discount
git merge feature/price-calculation
```

หรือถ้าต้องการใช้ rebase:

```bash
git checkout feature/price-calculation
git rebase feature/discount
```

### 2. เนื้อหาที่จะเห็นเมื่อเกิด conflict

```javascript
class PriceCalculator {
  constructor() {
    this.taxRate = 7; // 7% tax rate
  }

<<<<<<< HEAD
  calculatePrice(product) {
    return product.basePrice - product.discount;
  }
=======
  calculatePrice(product) {
    return product.basePrice * product.quantity;
  }
>>>>>>> feature/price-calculation

  calculateTax(product) {
    return this.calculatePrice(product) * (this.taxRate / 100);
  }

  calculateTotal(product) {
    return this.calculatePrice(product) + this.calculateTax(product);
  }
}

module.exports = PriceCalculator;
```

### 3. การแก้ไข conflict

แก้ไขไฟล์ให้รวมทั้งสองฟีเจอร์:

```javascript
class PriceCalculator {
  constructor() {
    this.taxRate = 7; // 7% tax rate
  }

  calculatePrice(product) {
    return (product.basePrice - product.discount) * product.quantity;
  }

  calculateTax(product) {
    return this.calculatePrice(product) * (this.taxRate / 100);
  }

  calculateTotal(product) {
    return this.calculatePrice(product) + this.calculateTax(product);
  }
}

module.exports = PriceCalculator;
```

### 4. คำสั่งสำหรับ commit การแก้ไข conflict

หลังจากแก้ไข conflict และบันทึกไฟล์แล้ว:

```bash
git add calculator.js
git commit
```

หรือถ้าใช้ rebase:

```bash
git add calculator.js
git rebase --continue
```

### 5. คำสั่งสำหรับยกเลิกการ merge

ถ้าใช้ merge:

```bash
git merge --abort
```

ถ้าใช้ rebase:

```bash
git rebase --abort
```

## ข้อแนะนำเพิ่มเติม

1. **ใช้เครื่องมือช่วยแก้ไข conflict**: หลาย IDE และ text editor มีเครื่องมือช่วยแก้ไข conflict ที่ทำให้การแก้ไขง่ายขึ้น

2. **ปรึกษากับเพื่อนร่วมทีม**: เมื่อเกิด conflict ที่ซับซ้อน ควรปรึกษากับเพื่อนร่วมทีมที่เขียนโค้ดส่วนนั้นเพื่อให้แน่ใจว่าการแก้ไขถูกต้อง

3. **ทดสอบหลังแก้ไข conflict**: หลังจากแก้ไข conflict แล้ว ควรทดสอบว่าโค้ดยังทำงานได้ถูกต้องหรือไม่

4. **พิจารณาใช้ feature flags**: ในโปรเจคใหญ่ การใช้ feature flags อาจช่วยลดโอกาสเกิด conflict ได้

## แบบฝึกหัดเพิ่มเติม

ลองทำตามขั้นตอนการจำลองสถานการณ์และแก้ไข conflict ด้วยตัวเอง จากนั้นลองเพิ่มฟีเจอร์ใหม่ เช่น การคำนวณค่าจัดส่ง ในอีก branch หนึ่ง และทดลองรวม branch เข้าด้วยกัน
