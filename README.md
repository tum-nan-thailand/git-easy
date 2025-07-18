# Git Easy: คู่มือเรียนรู้ Git สำหรับการทำงานในทีม

[![GitHub stars](https://img.shields.io/github/stars/yourusername/git-easy?style=social)](https://github.com/yourusername/git-easy/stargazers)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

<p align="center">
  <img src="https://git-scm.com/images/logos/downloads/Git-Logo-2Color.png" alt="Git Logo" width="200"/>
</p>

## 📖 เกี่ยวกับโครงการนี้

**Git Easy** เป็นคู่มือเรียนรู้ Git ฉบับภาษาไทยที่ครอบคลุมและเข้าใจง่าย ออกแบบมาเพื่อช่วยให้นักพัฒนาทุกระดับสามารถเข้าใจและใช้งาน Git ได้อย่างมีประสิทธิภาพในการทำงานเป็นทีม

คู่มือนี้มุ่งเน้นการแก้ไขปัญหาที่มักพบในการทำงานร่วมกัน โดยเฉพาะการจัดการกับ Git Conflict และการใช้คำสั่งขั้นสูงเช่น rebase, reset, stash ผ่านสถานการณ์จำลองและแบบฝึกหัดที่เป็นประโยชน์

## 🚀 ทำไมต้อง Git Easy?

- 🇹🇭 **เนื้อหาภาษาไทย** - เรียนรู้ได้ง่ายด้วยภาษาที่คุ้นเคย
- 🎯 **เน้นการปฏิบัติจริง** - ผ่านสถานการณ์จำลองและแบบฝึกหัดที่ใกล้เคียงกับการทำงานจริง
- 🧩 **ครอบคลุมสถานการณ์ที่ซับซ้อน** - เรียนรู้การแก้ไขปัญหาที่มักพบในการทำงานเป็นทีม
- 📈 **เหมาะกับทุกระดับ** - ตั้งแต่ผู้เริ่มต้นจนถึงนักพัฒนาที่มีประสบการณ์
- 🔄 **อัปเดตสม่ำเสมอ** - เนื้อหาทันสมัยตามการเปลี่ยนแปลงของ Git

## 📚 เนื้อหาในคู่มือ

### 📋 บทเรียนหลัก

1. [สถานการณ์จำลอง: Git Conflict](scenarios/conflict-scenario.md) - เรียนรู้วิธีการแก้ไข conflict ในสถานการณ์ต่างๆ
2. [การใช้งาน Git Rebase](guides/git-rebase-guide.md) - เข้าใจการใช้ rebase เพื่อจัดการประวัติ commit
3. [การใช้งาน Git Reset](guides/git-reset-guide.md) - เรียนรู้วิธีการย้อนกลับการเปลี่ยนแปลงด้วย reset
4. [การใช้งาน Git Stash](guides/git-stash-guide.md) - จัดการกับการเปลี่ยนแปลงชั่วคราวอย่างมีประสิทธิภาพ
5. [การแก้ไขปัญหา Git ขั้นสูง](guides/git-advanced-troubleshooting.md) - เรียนรู้เทคนิคการใช้ git bisect, reflog และ interactive rebase
6. [Git Workflow ที่นิยมใช้ในองค์กร](guides/git-workflows.md) - เปรียบเทียบ Git Flow, GitHub Flow, GitLab Flow และ Trunk-Based Development
7. [แบบฝึกหัดและเฉลย](exercises/README.md) - ทดสอบความเข้าใจด้วยแบบฝึกหัดที่หลากหลาย

### 🛠️ แบบฝึกหัดเฉพาะทาง

- [แบบฝึกหัด: การแก้ไข Git Conflict](exercises/exercise-conflict.md)
- [แบบฝึกหัด: การใช้งาน Git Rebase](exercises/exercise-rebase.md)
- [แบบฝึกหัด: การใช้งาน Git Reset](exercises/exercise-reset.md)
- [แบบฝึกหัด: การใช้งาน Git Stash](exercises/exercise-stash.md)
- [แบบฝึกหัด: สถานการณ์การทำงานเป็นทีม](exercises/exercise-team-workflow.md)

## 🚦 วิธีการใช้คู่มือ

1. **ศึกษาเนื้อหาตามลำดับ** - เริ่มจากพื้นฐานไปสู่หัวข้อที่ซับซ้อนขึ้น
2. **ทดลองทำตามสถานการณ์จำลอง** - ปฏิบัติตามขั้นตอนในเครื่องของคุณเพื่อเรียนรู้จากประสบการณ์จริง
3. **ทำแบบฝึกหัด** - ทดสอบความเข้าใจด้วยแบบฝึกหัดที่หลากหลาย
4. **ตรวจสอบเฉลย** - เปรียบเทียบวิธีการของคุณกับเฉลยเพื่อเรียนรู้เพิ่มเติม

## 🏁 เริ่มต้นใช้งาน

### การติดตั้ง Git

ก่อนเริ่มต้นใช้งานคู่มือนี้ คุณจำเป็นต้องติดตั้ง Git บนเครื่องของคุณ:

- **Windows**: ดาวน์โหลดและติดตั้งจาก [git-scm.com](https://git-scm.com/download/win)
- **macOS**: ติดตั้งผ่าน Homebrew: `brew install git`
- **Linux (Ubuntu/Debian)**: ติดตั้งผ่าน apt: `sudo apt-get install git`

### การตั้งค่าเริ่มต้น

```bash
# ตั้งค่าชื่อผู้ใช้
git config --global user.name "Your Name"

# ตั้งค่าอีเมล
git config --global user.email "your.email@example.com"
```

### เริ่มต้นเรียนรู้

เริ่มต้นจากการศึกษา [สถานการณ์จำลอง: Git Conflict](scenarios/conflict-scenario.md) เพื่อเข้าใจปัญหาที่มักพบในการทำงานร่วมกัน

## 🤝 การมีส่วนร่วมในโครงการ

เรายินดีรับการมีส่วนร่วมจากทุกคน! หากคุณต้องการช่วยปรับปรุงคู่มือนี้:

1. Fork repository นี้
2. สร้าง branch ใหม่: `git checkout -b feature/your-feature-name`
3. Commit การเปลี่ยนแปลงของคุณ: `git commit -m 'Add some feature'`
4. Push ไปยัง branch: `git push origin feature/your-feature-name`
5. สร้าง Pull Request

## 📜 ลิขสิทธิ์

โครงการนี้เผยแพร่ภายใต้ [MIT License](LICENSE) - ดูไฟล์ LICENSE สำหรับรายละเอียดเพิ่มเติม

## 📞 ติดต่อ

หากคุณมีคำถามหรือข้อเสนอแนะ สามารถติดต่อเราได้ที่ [punthaweeso@gmail.com](mailto:your.punthaweeso@gmail.com) หรือสร้าง Issue ใน repository นี้

