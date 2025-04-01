# คู่มือการใช้งาน Git Reset

Git Reset เป็นคำสั่งที่ทรงพลังสำหรับการย้อนกลับการเปลี่ยนแปลงและจัดการกับประวัติ commit คู่มือนี้จะอธิบายวิธีการใช้งาน Git Reset ประเภทต่างๆ และสถานการณ์ที่เหมาะสมในการใช้งาน

## Git Reset คืออะไร?

Git Reset เป็นคำสั่งที่ใช้ในการย้าย HEAD และ branch ปัจจุบันไปยัง commit อื่น โดยมีผลกระทบต่อ 3 ส่วนหลักของ Git:
1. **HEAD และ Branch Reference**: ตำแหน่งที่ HEAD และ branch ชี้ไป
2. **Index (Staging Area)**: การเปลี่ยนแปลงที่เตรียมจะ commit
3. **Working Directory**: ไฟล์ที่คุณกำลังทำงานอยู่

## ประเภทของ Git Reset

Git Reset มี 3 ประเภทหลัก ซึ่งแตกต่างกันที่ผลกระทบต่อ Index และ Working Directory:

### 1. `git reset --soft`

```bash
git reset --soft <commit>
```

- ย้าย HEAD และ branch ไปยัง commit ที่ระบุ
- **ไม่เปลี่ยนแปลง Index** (การเปลี่ยนแปลงยังคงอยู่ใน staging area)
- **ไม่เปลี่ยนแปลง Working Directory** (ไฟล์ยังคงเหมือนเดิม)

**เหมาะสำหรับ**: การรวม commits หลายอันเข้าด้วยกัน หรือการแก้ไข commit message ล่าสุด

**ตัวอย่างการใช้งาน**:
```bash
# ย้อนกลับไป 1 commit แต่เก็บการเปลี่ยนแปลงไว้ใน staging area
git reset --soft HEAD~1

# จากนั้นทำการ commit ใหม่
git commit -m "New commit message"
```

### 2. `git reset --mixed` (ค่าเริ่มต้น)

```bash
git reset <commit>
# หรือ
git reset --mixed <commit>
```

- ย้าย HEAD และ branch ไปยัง commit ที่ระบุ
- **เปลี่ยนแปลง Index** ให้ตรงกับ commit ที่ระบุ (การเปลี่ยนแปลงจะถูกนำออกจาก staging area)
- **ไม่เปลี่ยนแปลง Working Directory** (ไฟล์ยังคงเหมือนเดิม)

**เหมาะสำหรับ**: การยกเลิกการ stage ไฟล์ หรือการเริ่มต้น commit ใหม่จากการเปลี่ยนแปลงเดิม

**ตัวอย่างการใช้งาน**:
```bash
# ย้อนกลับไป 1 commit และนำการเปลี่ยนแปลงออกจาก staging area
git reset HEAD~1

# จากนั้นคุณสามารถเลือก stage ไฟล์ที่ต้องการและ commit ใหม่
git add file1.js file2.js
git commit -m "More selective commit"
```

### 3. `git reset --hard`

```bash
git reset --hard <commit>
```

- ย้าย HEAD และ branch ไปยัง commit ที่ระบุ
- **เปลี่ยนแปลง Index** ให้ตรงกับ commit ที่ระบุ
- **เปลี่ยนแปลง Working Directory** ให้ตรงกับ commit ที่ระบุ (การเปลี่ยนแปลงทั้งหมดจะหายไป)

**เหมาะสำหรับ**: การยกเลิกการเปลี่ยนแปลงทั้งหมดและกลับไปยัง commit ก่อนหน้า

**ข้อควรระวัง**: การใช้ `--hard` จะลบการเปลี่ยนแปลงที่ยังไม่ได้ commit ทั้งหมด ควรใช้ด้วยความระมัดระวัง

**ตัวอย่างการใช้งาน**:
```bash
# ย้อนกลับไป 1 commit และลบการเปลี่ยนแปลงทั้งหมด
git reset --hard HEAD~1

# หรือกลับไปยัง commit เฉพาะ
git reset --hard abc123
```

## การใช้งาน Git Reset ในสถานการณ์ต่างๆ

### 1. การยกเลิกการ Stage ไฟล์

```bash
# ยกเลิกการ stage ไฟล์ทั้งหมด
git reset

# ยกเลิกการ stage ไฟล์เฉพาะ
git reset -- file1.js file2.js
```

### 2. การยกเลิก Commit ล่าสุด แต่เก็บการเปลี่ยนแปลงไว้

```bash
git reset --soft HEAD~1
```

### 3. การยกเลิก Commits หลายอัน แต่เก็บการเปลี่ยนแปลงไว้

```bash
# ยกเลิก 3 commits ล่าสุด แต่เก็บการเปลี่ยนแปลงไว้
git reset --soft HEAD~3
```

### 4. การยกเลิกการเปลี่ยนแปลงทั้งหมดและกลับไปยัง Commit เฉพาะ

```bash
git reset --hard <commit-hash>
```

### 5. การยกเลิกการ Merge ที่ยังไม่ได้ Push

```bash
# สมมติว่าคุณเพิ่ง merge และยังไม่ได้ push
git reset --hard ORIG_HEAD
```

## การกู้คืนหลังจากใช้ Git Reset --hard

หากคุณใช้ `git reset --hard` และต้องการกู้คืนการเปลี่ยนแปลง คุณสามารถใช้ `git reflog` เพื่อค้นหา commit ที่ถูกลบ:

```bash
# ดูประวัติการเปลี่ยนแปลง HEAD
git reflog

# สมมติว่าคุณพบ commit ที่ต้องการที่ hash abc123
git checkout abc123

# สร้าง branch ใหม่จาก commit นั้น
git checkout -b recovery-branch
```

## ความแตกต่างระหว่าง Git Reset และ Git Revert

### Git Reset
- เปลี่ยนแปลงประวัติ commit โดยการย้าย HEAD และ branch
- เหมาะสำหรับการแก้ไข commits ที่ยังไม่ได้ push
- อาจทำให้เกิดปัญหาถ้าใช้กับ commits ที่ push แล้ว

### Git Revert
- สร้าง commit ใหม่ที่ยกเลิกการเปลี่ยนแปลงของ commit ที่ระบุ
- ไม่เปลี่ยนแปลงประวัติ commit ที่มีอยู่แล้ว
- เหมาะสำหรับการยกเลิกการเปลี่ยนแปลงที่ push แล้ว

```bash
# ยกเลิกการเปลี่ยนแปลงของ commit ล่าสุด
git revert HEAD

# ยกเลิกการเปลี่ยนแปลงของ commit เฉพาะ
git revert abc123
```

## สถานการณ์ที่ควรใช้ Git Reset ในการทำงานเป็นทีม

### 1. แก้ไข Commit ล่าสุดก่อน Push

```bash
# แก้ไขไฟล์เพิ่มเติม
git add file.js
git reset --soft HEAD~1
git commit -m "Better commit message with additional changes"
```

### 2. แยก Commit ใหญ่เป็น Commits เล็กๆ

```bash
git reset --mixed HEAD~1
# จากนั้น stage และ commit ทีละส่วน
git add part1.js
git commit -m "Part 1 changes"
git add part2.js
git commit -m "Part 2 changes"
```

### 3. ล้าง Branch ที่มีปัญหาและเริ่มใหม่

```bash
# สมมติว่า branch ปัจจุบันมีปัญหา
git fetch origin
git reset --hard origin/main
git checkout -b feature-branch-new
```

## ข้อควรระวังเมื่อใช้ Git Reset

1. **อย่าใช้ Reset กับ Commits ที่ Push แล้ว**: การ reset commits ที่ push แล้วจะทำให้เกิดปัญหากับผู้อื่นที่ใช้ branch เดียวกัน

2. **ระวังการใช้ `--hard`**: การใช้ `--hard` จะลบการเปลี่ยนแปลงที่ยังไม่ได้ commit ทั้งหมด ควรตรวจสอบให้แน่ใจว่าคุณต้องการทำเช่นนั้นจริงๆ

3. **ใช้ Git Stash ก่อน Reset ถ้าไม่แน่ใจ**: ถ้าคุณไม่แน่ใจว่าต้องการเก็บการเปลี่ยนแปลงหรือไม่ ให้ใช้ `git stash` ก่อน

```bash
git stash
git reset --hard HEAD~3
# ถ้าต้องการกู้คืนการเปลี่ยนแปลง
git stash pop
```

## สรุป

Git Reset เป็นเครื่องมือที่ทรงพลังสำหรับการจัดการกับประวัติ commit และการเปลี่ยนแปลง การเข้าใจความแตกต่างระหว่าง `--soft`, `--mixed`, และ `--hard` จะช่วยให้คุณสามารถเลือกใช้ได้อย่างเหมาะสมกับสถานการณ์ต่างๆ อย่างไรก็ตาม ควรใช้ด้วยความระมัดระวัง โดยเฉพาะกับ commits ที่ push แล้ว

ในหัวข้อถัดไป เราจะเรียนรู้เกี่ยวกับ [Git Stash](git-stash-guide.md) ซึ่งเป็นอีกหนึ่งคำสั่งที่มีประโยชน์สำหรับการจัดการกับการเปลี่ยนแปลงชั่วคราว
