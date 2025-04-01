# การแก้ไขปัญหา Git ขั้นสูง

บทเรียนนี้จะแนะนำเทคนิคขั้นสูงในการแก้ไขปัญหาและจัดการกับสถานการณ์ซับซ้อนใน Git ที่นักพัฒนามักพบเมื่อทำงานกับโปรเจคขนาดใหญ่หรือทีมที่มีหลายคน

## 🔍 การใช้ git bisect เพื่อค้นหา bug

`git bisect` เป็นเครื่องมือทรงพลังที่ช่วยให้คุณค้นหา commit ที่ทำให้เกิด bug ได้อย่างรวดเร็ว โดยใช้วิธีการค้นหาแบบ binary search

### หลักการทำงานของ git bisect

1. คุณบอก Git ว่า commit ปัจจุบันมี bug
2. คุณบอก Git ว่ามี commit เก่าที่ยังไม่มี bug
3. Git จะเลือก commit ตรงกลางระหว่างสองจุดนี้ให้คุณทดสอบ
4. คุณบอก Git ว่า commit นี้มี bug หรือไม่
5. Git จะเลือก commit ถัดไปให้ทดสอบ โดยแบ่งครึ่งช่วงที่เหลือ
6. ทำซ้ำจนกว่าจะพบ commit ที่ทำให้เกิด bug

### ขั้นตอนการใช้งาน git bisect

```bash
# เริ่มต้นกระบวนการ bisect
git bisect start

# ระบุว่า commit ปัจจุบันมี bug
git bisect bad

# ระบุ commit เก่าที่ยังทำงานได้ดี
git bisect good v1.0.0

# Git จะ checkout commit ตรงกลาง
# ทดสอบโค้ดว่ามี bug หรือไม่
# ถ้ามี bug:
git bisect bad

# ถ้าไม่มี bug:
git bisect good

# ทำซ้ำจนกว่า Git จะบอกว่าพบ commit ที่ทำให้เกิด bug
# เมื่อเสร็จสิ้น:
git bisect reset
```

### ตัวอย่างการใช้งานจริง

สมมติว่าเรามี bug ที่ทำให้ feature การล็อกอินไม่ทำงาน แต่เราไม่รู้ว่าเกิดขึ้นตั้งแต่เมื่อไหร่:

```bash
# เริ่มต้น bisect
git bisect start

# commit ปัจจุบันมี bug
git bisect bad

# เราเชื่อว่า 2 สัปดาห์ที่แล้วยังทำงานได้ดี
git bisect good HEAD~100

# Git จะ checkout commit ตรงกลาง
# ทดสอบฟีเจอร์การล็อกอิน
# สมมติว่ายังมี bug
git bisect bad

# Git จะเลือก commit ถัดไป
# ทดสอบอีกครั้ง
# สมมติว่าไม่มี bug
git bisect good

# ทำซ้ำจนกว่า Git จะบอกว่าพบ commit ที่ทำให้เกิด bug
# เช่น: b1a534c is the first bad commit
```

### การใช้ git bisect แบบอัตโนมัติ

หากคุณมีสคริปต์ทดสอบที่สามารถตรวจสอบ bug ได้ คุณสามารถให้ Git ทำงานอัตโนมัติได้:

```bash
git bisect start HEAD v1.0.0
git bisect run ./test-script.sh
```

สคริปต์ควรส่งค่า exit code 0 ถ้าไม่มี bug และค่าอื่นๆ (1-127) ถ้ามี bug

## 🔄 การใช้ git reflog เพื่อกู้คืนการเปลี่ยนแปลงที่หายไป

`git reflog` เป็นเครื่องมือที่ช่วยให้คุณดูประวัติการเปลี่ยนแปลงของ HEAD ทั้งหมด รวมถึงการเปลี่ยนแปลงที่ไม่ปรากฏใน git log เช่น การ reset, rebase หรือ branch ที่ถูกลบ

### สถานการณ์ที่ต้องใช้ git reflog

- คุณทำ `git reset --hard` แล้วเปลี่ยนใจ ต้องการกู้คืนการเปลี่ยนแปลง
- คุณลบ branch โดยไม่ได้ตั้งใจ
- คุณทำ rebase แล้วสูญเสีย commit บางส่วน
- คุณทำการเปลี่ยนแปลงที่ซับซ้อนและต้องการย้อนกลับไปยังสถานะก่อนหน้า

### วิธีใช้ git reflog

```bash
# ดูประวัติการเปลี่ยนแปลงทั้งหมด
git reflog

# ตัวอย่างผลลัพธ์:
# 734713b HEAD@{0}: commit: Add feature X
# 87d8cc1 HEAD@{1}: pull: Fast-forward
# 23a9d42 HEAD@{2}: reset: moving to HEAD~3
# ...
```

### การกู้คืน commit ที่หายไปหลังจาก reset

```bash
# สมมติว่าคุณทำ git reset --hard และต้องการกู้คืน
git reflog
# พบว่าก่อน reset HEAD อยู่ที่ 23a9d42

# กู้คืนโดยสร้าง branch ใหม่ที่ชี้ไปยัง commit นั้น
git branch recover-branch 23a9d42

# หรือย้าย HEAD ไปยัง commit นั้นโดยตรง
git reset --hard 23a9d42
```

### การกู้คืน branch ที่ถูกลบ

```bash
# สมมติว่าคุณลบ branch feature-x โดยไม่ได้ตั้งใจ
git reflog
# พบว่า commit สุดท้ายของ branch นั้นคือ 734713b

# สร้าง branch ใหม่จาก commit นั้น
git branch feature-x-recovered 734713b
```

### เทคนิคการใช้ git reflog ขั้นสูง

```bash
# ดู reflog เฉพาะของ branch ใดบรานช์หนึ่ง
git reflog show feature-branch

# ดู reflog พร้อมรายละเอียดเพิ่มเติม
git reflog --date=iso

# กู้คืนไฟล์เฉพาะที่ถูกลบหลังจาก reset
git checkout HEAD@{1} -- path/to/file.txt
```

## 🔀 การทำ interactive rebase เพื่อจัดการประวัติ commit

Interactive rebase เป็นเครื่องมือที่ทรงพลังสำหรับการจัดการและปรับแต่งประวัติ commit ก่อนที่จะ push ไปยัง remote repository

### สิ่งที่คุณสามารถทำได้ด้วย interactive rebase

- รวม (squash) หลาย commit เข้าด้วยกัน
- แก้ไขข้อความ commit
- แก้ไขลำดับของ commit
- แยก commit ออกเป็นหลาย commit
- ลบ commit ที่ไม่ต้องการ
- แก้ไขเนื้อหาของ commit เก่า

### ข้อควรระวัง

⚠️ **คำเตือน**: ไม่ควรใช้ interactive rebase กับ commit ที่ push ไปยัง remote repository แล้ว เว้นแต่คุณทำงานคนเดียวหรือได้แจ้งทีมแล้ว เพราะจะทำให้ประวัติ commit เปลี่ยนไปและสร้างปัญหาให้ผู้อื่น

### วิธีใช้ interactive rebase

```bash
# เริ่ม interactive rebase ย้อนหลังไป 3 commit
git rebase -i HEAD~3

# หรือระบุ commit เริ่มต้น
git rebase -i 7f545f6
```

เมื่อคุณรัน command นี้ Git จะเปิดตัวแก้ไขข้อความพร้อมรายการ commit และคำสั่งที่สามารถใช้ได้:

```
pick 7f545f6 Add feature X
pick 9d3b283 Fix bug in feature X
pick c4d92f5 Update documentation

# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

### ตัวอย่างการใช้งาน interactive rebase

#### การรวม commit (squash)

```
pick 7f545f6 Add feature X
squash 9d3b283 Fix bug in feature X
squash c4d92f5 Update documentation
```

เมื่อบันทึกและปิดไฟล์ Git จะรวม 3 commit เข้าด้วยกันและให้คุณแก้ไขข้อความ commit ใหม่

#### การแก้ไขข้อความ commit

```
pick 7f545f6 Add feature X
reword 9d3b283 Fix bug in feature X
pick c4d92f5 Update documentation
```

Git จะหยุดที่ commit 9d3b283 และให้คุณแก้ไขข้อความ commit

#### การลบ commit

```
pick 7f545f6 Add feature X
drop 9d3b283 Fix bug in feature X
pick c4d92f5 Update documentation
```

Git จะลบ commit 9d3b283 ออกจากประวัติ

#### การแก้ไขเนื้อหาของ commit เก่า

```
pick 7f545f6 Add feature X
edit 9d3b283 Fix bug in feature X
pick c4d92f5 Update documentation
```

Git จะหยุดที่ commit 9d3b283 และให้คุณแก้ไขไฟล์ จากนั้นใช้:

```bash
git add .
git commit --amend
git rebase --continue
```

### เทคนิคขั้นสูงสำหรับ interactive rebase

#### การแยก commit

```bash
# เลือก edit ใน interactive rebase
# เมื่อ Git หยุดที่ commit ที่ต้องการแยก:
git reset HEAD^
# ตอนนี้การเปลี่ยนแปลงทั้งหมดจะอยู่ใน working directory
# commit ทีละส่วน:
git add file1.js
git commit -m "First part of the change"
git add file2.js
git commit -m "Second part of the change"
# เมื่อเสร็จแล้ว:
git rebase --continue
```

#### การเปลี่ยนลำดับ commit

เพียงแค่เปลี่ยนลำดับบรรทัดใน interactive rebase:

```
pick c4d92f5 Update documentation
pick 7f545f6 Add feature X
pick 9d3b283 Fix bug in feature X
```

#### การใช้ exec เพื่อทดสอบระหว่าง rebase

```
pick 7f545f6 Add feature X
exec npm test
pick 9d3b283 Fix bug in feature X
exec npm test
pick c4d92f5 Update documentation
exec npm test
```

Git จะรัน `npm test` หลังจากแต่ละ commit เพื่อให้แน่ใจว่าทุก commit ผ่านการทดสอบ

## 🔧 สรุปเครื่องมือแก้ไขปัญหา Git ขั้นสูง

| เครื่องมือ | ใช้เมื่อ | ข้อควรระวัง |
|-----------|---------|------------|
| git bisect | ต้องการค้นหา commit ที่ทำให้เกิด bug | ต้องมีวิธีทดสอบที่ชัดเจนว่ามี bug หรือไม่ |
| git reflog | ต้องการกู้คืนการเปลี่ยนแปลงที่หายไป | reflog มีอายุจำกัด (ค่าเริ่มต้น 90 วัน) |
| interactive rebase | ต้องการจัดการประวัติ commit | ไม่ควรใช้กับ commit ที่ push แล้ว |

การเรียนรู้เครื่องมือเหล่านี้จะช่วยให้คุณสามารถแก้ไขปัญหาซับซ้อนใน Git และทำงานได้อย่างมีประสิทธิภาพมากขึ้น

## 📚 แหล่งเรียนรู้เพิ่มเติม

- [Git Documentation - git-bisect](https://git-scm.com/docs/git-bisect)
- [Git Documentation - git-reflog](https://git-scm.com/docs/git-reflog)
- [Git Documentation - git-rebase](https://git-scm.com/docs/git-rebase)
- [Pro Git Book - Rewriting History](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)
