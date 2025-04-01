# คู่มือการใช้งาน Git Stash

Git Stash เป็นคำสั่งที่ช่วยให้คุณสามารถเก็บการเปลี่ยนแปลงชั่วคราวไว้ได้ โดยไม่ต้อง commit คู่มือนี้จะอธิบายวิธีการใช้งาน Git Stash และสถานการณ์ที่เหมาะสมในการใช้งาน

## Git Stash คืออะไร?

Git Stash เป็นคำสั่งที่ใช้ในการเก็บการเปลี่ยนแปลงที่ยังไม่ได้ commit ไว้ชั่วคราว ทำให้คุณสามารถสลับไปทำงานอื่นและกลับมาทำงานเดิมได้ในภายหลัง โดยไม่ต้องทำการ commit ที่ยังไม่เสร็จสมบูรณ์

## คำสั่ง Git Stash พื้นฐาน

### การเก็บการเปลี่ยนแปลงไว้ชั่วคราว

```bash
git stash
# หรือ
git stash save "ข้อความอธิบาย"
```

คำสั่งนี้จะเก็บการเปลี่ยนแปลงทั้งหมดที่ยังไม่ได้ commit (ทั้งที่อยู่ใน staging area และไม่ได้อยู่ใน staging area) และทำให้ working directory กลับไปเป็นสถานะเดียวกับ HEAD

### การดูรายการ Stash

```bash
git stash list
```

ผลลัพธ์จะแสดงรายการ stash ทั้งหมด เช่น:
```
stash@{0}: WIP on feature-branch: abc123 Last commit message
stash@{1}: On main: Fix bug in login form
```

### การนำการเปลี่ยนแปลงกลับมา

มี 2 วิธีในการนำการเปลี่ยนแปลงจาก stash กลับมา:

#### 1. `git stash apply` - นำการเปลี่ยนแปลงกลับมาแต่ยังเก็บไว้ใน stash

```bash
# นำ stash ล่าสุดกลับมา
git stash apply

# นำ stash เฉพาะกลับมา
git stash apply stash@{2}
```

#### 2. `git stash pop` - นำการเปลี่ยนแปลงกลับมาและลบออกจาก stash

```bash
# นำ stash ล่าสุดกลับมาและลบออกจาก stash
git stash pop

# นำ stash เฉพาะกลับมาและลบออกจาก stash
git stash pop stash@{2}
```

### การลบ Stash

```bash
# ลบ stash เฉพาะ
git stash drop stash@{1}

# ลบ stash ทั้งหมด
git stash clear
```

## การใช้งาน Git Stash ขั้นสูง

### การ Stash เฉพาะบางไฟล์

```bash
git stash push -m "Stash specific files" file1.js file2.js
```

### การ Stash เฉพาะการเปลี่ยนแปลงที่ไม่ได้อยู่ใน Staging Area

```bash
git stash --keep-index
```

### การ Stash รวมถึงไฟล์ที่ยังไม่ได้ Track

```bash
git stash -u
# หรือ
git stash --include-untracked
```

### การสร้าง Branch ใหม่จาก Stash

```bash
git stash branch new-branch-name stash@{1}
```

คำสั่งนี้จะสร้าง branch ใหม่จาก commit ที่ stash ถูกสร้างขึ้น นำการเปลี่ยนแปลงจาก stash มาใช้ และลบ stash นั้นถ้าสำเร็จ

### การดูความแตกต่างของ Stash

```bash
# เปรียบเทียบ stash กับ working directory ปัจจุบัน
git stash show -p

# เปรียบเทียบ stash เฉพาะกับ working directory ปัจจุบัน
git stash show -p stash@{1}
```

## สถานการณ์ที่ควรใช้ Git Stash

### 1. เมื่อต้องการสลับไปทำงานอื่นอย่างเร่งด่วน

```bash
# เก็บงานปัจจุบันไว้
git stash save "WIP on feature X"

# สลับไปทำงานอื่น
git checkout hotfix-branch
# แก้ไขและ commit

# กลับมาทำงานเดิม
git checkout feature-branch
git stash pop
```

### 2. เมื่อต้องการ Pull การเปลี่ยนแปลงจาก Remote แต่มีการเปลี่ยนแปลงในเครื่อง

```bash
git stash
git pull
git stash pop
```

### 3. เมื่อต้องการทดสอบว่าโค้ดทำงานได้ดีหรือไม่โดยไม่มีการเปลี่ยนแปลงปัจจุบัน

```bash
git stash
# ทดสอบโค้ด
git stash pop
```

### 4. เมื่อต้องการนำการเปลี่ยนแปลงไปใช้ใน Branch อื่น

```bash
# เก็บการเปลี่ยนแปลงไว้
git stash

# สลับไป branch อื่น
git checkout other-branch

# นำการเปลี่ยนแปลงมาใช้
git stash pop
```

## การแก้ไข Conflict เมื่อใช้ Git Stash

เมื่อคุณใช้ `git stash pop` หรือ `git stash apply` และเกิด conflict:

1. Git จะแจ้งเตือนว่ามี conflict และหยุดกระบวนการ
2. คุณต้องแก้ไข conflict เหมือนกับการ merge ปกติ
3. หลังจากแก้ไข conflict แล้ว ให้ทำการ add ไฟล์ที่แก้ไขแล้ว

```bash
git stash pop
# เกิด conflict
# แก้ไข conflict
git add <conflicted-files>
```

**หมายเหตุ**: ถ้าคุณใช้ `git stash pop` และเกิด conflict, stash จะยังคงอยู่ในรายการ stash จนกว่าคุณจะลบมันด้วย `git stash drop`

## เทคนิคการใช้ Git Stash ในการทำงานเป็นทีม

### 1. ใช้ข้อความอธิบายที่ชัดเจน

```bash
git stash save "Fix login bug - incomplete"
```

### 2. ใช้ Stash เป็นทางเลือกแทน Branch สำหรับการเปลี่ยนแปลงเล็กๆ น้อยๆ

```bash
# เก็บการเปลี่ยนแปลงเล็กๆ น้อยๆ
git stash save "Small UI tweaks"

# ทำงานอื่นๆ
# ...

# กลับมาทำงานเดิม
git stash pop
```

### 3. ใช้ร่วมกับ Git Worktree สำหรับการทำงานหลาย Tasks พร้อมกัน

```bash
# สร้าง worktree ใหม่
git worktree add ../project-hotfix hotfix-branch

# ทำงานใน worktree หลัก
# เมื่อต้องการสลับไปทำ hotfix
cd ../project-hotfix
# ทำงานและ commit

# กลับมาที่ worktree หลัก
cd ../project
```

## ข้อควรระวังเมื่อใช้ Git Stash

1. **Stash ไม่ควรใช้เก็บการเปลี่ยนแปลงระยะยาว**: Stash เหมาะสำหรับการเก็บการเปลี่ยนแปลงชั่วคราวเท่านั้น สำหรับการเปลี่ยนแปลงที่ต้องการเก็บไว้นาน ควรใช้ branch

2. **ระวังการใช้ `git stash pop` เมื่อมีโอกาสเกิด Conflict**: ถ้าคุณคาดว่าอาจเกิด conflict ควรใช้ `git stash apply` แทน เพื่อให้ stash ยังคงอยู่ในกรณีที่มีปัญหา

3. **ตรวจสอบรายการ Stash อยู่เสมอ**: ใช้ `git stash list` เพื่อตรวจสอบว่ามี stash ที่ลืมไว้หรือไม่

4. **ลบ Stash ที่ไม่ใช้แล้ว**: เพื่อป้องกันความสับสน ควรลบ stash ที่ไม่ได้ใช้แล้วด้วย `git stash drop` หรือใช้ `git stash pop` แทน `git stash apply`

## สรุป

Git Stash เป็นเครื่องมือที่มีประโยชน์มากสำหรับการจัดการกับการเปลี่ยนแปลงชั่วคราว ช่วยให้คุณสามารถสลับไปทำงานอื่นได้อย่างรวดเร็วโดยไม่ต้องทำการ commit ที่ยังไม่เสร็จสมบูรณ์ การเข้าใจวิธีการใช้งาน Git Stash อย่างถูกต้องจะช่วยให้การทำงานกับ Git มีประสิทธิภาพมากขึ้น

ในหัวข้อถัดไป เราจะทำแบบฝึกหัดเพื่อทดสอบความเข้าใจเกี่ยวกับ Git Conflict, Rebase, Reset และ Stash ที่เราได้เรียนรู้มา
