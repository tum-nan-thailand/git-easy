# คู่มือการใช้งาน Git Rebase

Git Rebase เป็นคำสั่งที่ทรงพลังแต่อาจทำให้สับสนได้ง่าย คู่มือนี้จะช่วยให้คุณเข้าใจวิธีการใช้งาน Git Rebase อย่างถูกต้องและปลอดภัย

## Git Rebase คืออะไร?

Rebase คือการย้ายหรือรวมชุดของ commits จาก branch หนึ่งไปยังจุดใหม่ใน branch อื่น โดยมีวัตถุประสงค์หลักคือ:
1. ทำให้ประวัติ commit เรียบง่ายและเป็นเส้นตรง
2. อัพเดท feature branch ให้ทันสมัยกับ main branch
3. แก้ไข commits ก่อนที่จะ push ไปยัง remote repository

## ความแตกต่างระหว่าง Merge และ Rebase

### Merge
- สร้าง commit ใหม่ที่รวมการเปลี่ยนแปลงจากทั้งสอง branches
- รักษาประวัติ commit ทั้งหมดไว้
- ไม่เปลี่ยนแปลง commit ที่มีอยู่แล้ว

### Rebase
- ย้าย commits จาก branch ปัจจุบันไปต่อท้าย branch เป้าหมาย
- สร้าง commit ใหม่ที่มีการเปลี่ยนแปลงเหมือนกับ commit เดิม แต่มี hash ใหม่
- ทำให้ประวัติ commit เป็นเส้นตรง

## คำสั่ง Git Rebase พื้นฐาน

### การ Rebase Branch ปัจจุบันกับ Branch อื่น

```bash
# สมมติว่าคุณอยู่ใน feature branch และต้องการ rebase กับ main
git checkout feature
git rebase main
```

### การแก้ไข Conflict ระหว่าง Rebase

เมื่อเกิด conflict ระหว่างการ rebase:

1. Git จะหยุดกระบวนการ rebase และแจ้งให้คุณแก้ไข conflict
2. แก้ไขไฟล์ที่มี conflict
3. ทำการ add ไฟล์ที่แก้ไขแล้ว
4. ใช้คำสั่ง `git rebase --continue` เพื่อดำเนินการ rebase ต่อ

```bash
# หลังจากแก้ไข conflict
git add <conflicted-file>
git rebase --continue
```

### การยกเลิก Rebase

หากคุณต้องการยกเลิกกระบวนการ rebase:

```bash
git rebase --abort
```

## การใช้งาน Git Rebase ขั้นสูง

### Interactive Rebase

Interactive rebase ช่วยให้คุณสามารถแก้ไข, รวม, หรือลบ commits ก่อนที่จะทำการ rebase

```bash
# Rebase 3 commits ล่าสุดแบบ interactive
git rebase -i HEAD~3
```

คำสั่งนี้จะเปิด editor ที่แสดงรายการ commits พร้อมคำสั่งที่สามารถใช้ได้:

```
pick f7f3f6d commit message 1
pick 310154e commit message 2
pick a5f4a0d commit message 3
```

คำสั่งที่สามารถใช้ได้:
- `pick`: ใช้ commit นี้
- `reword`: ใช้ commit นี้แต่แก้ไขข้อความ commit
- `edit`: ใช้ commit นี้แต่หยุดเพื่อแก้ไข
- `squash`: ใช้ commit นี้แต่รวมกับ commit ก่อนหน้า
- `fixup`: เหมือน squash แต่ไม่เก็บข้อความ commit
- `drop`: ลบ commit นี้

### การใช้ `git rebase --onto`

คำสั่ง `--onto` ช่วยให้คุณสามารถย้าย commits จาก branch หนึ่งไปยังอีก branch หนึ่งได้อย่างแม่นยำ:

```bash
git rebase --onto <new-base> <old-base> <branch>
```

ตัวอย่าง:
```bash
# ย้าย commits จาก feature-branch ที่อยู่หลัง main ไปยัง release-branch
git rebase --onto release-branch main feature-branch
```

## ข้อควรระวังเมื่อใช้ Git Rebase

1. **อย่า Rebase Commits ที่ Push แล้ว**: การ rebase จะสร้าง commits ใหม่ ถ้าคุณ rebase commits ที่ push แล้ว จะทำให้เกิดปัญหากับผู้อื่นที่ใช้ branch เดียวกัน

2. **ใช้ `git pull --rebase`**: เมื่อต้องการดึงการเปลี่ยนแปลงจาก remote และ rebase กับ branch ปัจจุบัน

3. **ทำความเข้าใจก่อนใช้ Interactive Rebase**: การแก้ไข commit history อาจทำให้เกิดปัญหาได้ถ้าไม่เข้าใจผลลัพธ์

## สถานการณ์ที่ควรใช้ Git Rebase

1. **ก่อน Push Feature Branch**: เพื่อทำให้ประวัติ commit สะอาดก่อนที่จะ push

2. **อัพเดท Feature Branch**: เมื่อ main branch มีการเปลี่ยนแปลงและคุณต้องการให้ feature branch ทันสมัย

3. **แก้ไข Commit Messages**: เมื่อต้องการแก้ไขข้อความ commit ก่อนที่จะ push

4. **รวม Commits**: เมื่อต้องการรวม commits หลายๆ อันเข้าด้วยกันเพื่อให้ประวัติ commit สะอาดขึ้น

## การแก้ไขปัญหาเมื่อ `git rebase --continue` ไม่ทำงาน

บางครั้งหลังจากแก้ไข conflict และใช้คำสั่ง `git rebase --continue` แล้ว อาจเกิดปัญหาต่างๆ ดังนี้:

### 1. ยังมี Conflict ที่ยังไม่ได้แก้ไข

ตรวจสอบว่าคุณได้แก้ไข conflict ทั้งหมดแล้ว:

```bash
git status
```

ถ้ายังมีไฟล์ที่มี conflict ให้แก้ไขและทำการ add:

```bash
git add <conflicted-file>
```

### 2. ไม่ได้ Add ไฟล์หลังแก้ไข Conflict

ต้องทำการ add ไฟล์ทุกไฟล์ที่มีการแก้ไข:

```bash
git add .
git rebase --continue
```

### 3. มีการเปลี่ยนแปลงที่ยังไม่ได้ Commit

บางครั้งคุณอาจต้องทำการ commit การเปลี่ยนแปลงก่อนที่จะ continue:

```bash
git commit -m "Fix conflict"
git rebase --continue
```

### 4. ปัญหาอื่นๆ

ถ้ายังมีปัญหา คุณสามารถยกเลิก rebase และเริ่มใหม่:

```bash
git rebase --abort
```

## สรุป

Git Rebase เป็นเครื่องมือที่ทรงพลังสำหรับการจัดการประวัติ commit ให้สะอาดและเป็นระเบียบ แต่ต้องใช้อย่างระมัดระวัง โดยเฉพาะกับ commits ที่ push แล้ว การเข้าใจวิธีการทำงานของ rebase และวิธีการแก้ไข conflicts จะช่วยให้คุณสามารถใช้งาน Git ได้อย่างมีประสิทธิภาพมากขึ้น

ในหัวข้อถัดไป เราจะเรียนรู้เกี่ยวกับ [Git Reset](git-reset-guide.md) ซึ่งเป็นอีกหนึ่งคำสั่งที่ทรงพลังสำหรับการจัดการกับ commits
