# บทเรียนเกี่ยวกับ Git Workflow ที่นิยมใช้ในองค์กร

การทำงานเป็นทีมกับ Git จำเป็นต้องมีแนวทางและกระบวนการที่ชัดเจน บทเรียนนี้จะแนะนำ Git Workflow รูปแบบต่างๆ ที่นิยมใช้ในองค์กรและทีมพัฒนาซอฟต์แวร์ทั่วโลก

## 🌳 Git Flow

Git Flow เป็น workflow ที่ได้รับความนิยมมากที่สุดรูปแบบหนึ่ง ออกแบบโดย Vincent Driessen ในปี 2010 เหมาะสำหรับโปรเจคที่มีรอบการพัฒนาและการ release ที่ชัดเจน

### โครงสร้างของ Git Flow

Git Flow ประกอบด้วย branch หลัก 5 ประเภท:

1. **main/master**: เก็บโค้ดที่พร้อมใช้งานในระบบ production
2. **develop**: branch หลักสำหรับการพัฒนา รวบรวมฟีเจอร์ที่เสร็จแล้ว
3. **feature/\***: branch สำหรับพัฒนาฟีเจอร์ใหม่ แยกออกจาก develop
4. **release/\***: branch สำหรับเตรียม release แยกออกจาก develop
5. **hotfix/\***: branch สำหรับแก้ไขบัคเร่งด่วนใน production แยกออกจาก main

### ขั้นตอนการทำงานใน Git Flow

#### การพัฒนาฟีเจอร์ใหม่

```bash
# เริ่มจาก develop branch
git checkout develop
git pull

# สร้าง feature branch
git checkout -b feature/user-authentication

# พัฒนาฟีเจอร์และ commit การเปลี่ยนแปลง
git add .
git commit -m "Add user authentication feature"

# เมื่อพัฒนาเสร็จแล้ว merge กลับไปยัง develop
git checkout develop
git merge --no-ff feature/user-authentication
git push origin develop

# ลบ feature branch (optional)
git branch -d feature/user-authentication
```

#### การสร้าง Release

```bash
# สร้าง release branch จาก develop
git checkout develop
git checkout -b release/1.0.0

# แก้ไขเวอร์ชันและทำการทดสอบ
git commit -m "Bump version to 1.0.0"

# เมื่อพร้อม release ให้ merge เข้า main และ develop
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"
git push origin main --tags

git checkout develop
git merge --no-ff release/1.0.0
git push origin develop

# ลบ release branch
git branch -d release/1.0.0
```

#### การแก้ไข Hotfix

```bash
# สร้าง hotfix branch จาก main
git checkout main
git checkout -b hotfix/1.0.1

# แก้ไขบัคและเพิ่มเวอร์ชัน
git commit -m "Fix critical bug"
git commit -m "Bump version to 1.0.1"

# merge เข้า main และ develop
git checkout main
git merge --no-ff hotfix/1.0.1
git tag -a v1.0.1 -m "Version 1.0.1"
git push origin main --tags

git checkout develop
git merge --no-ff hotfix/1.0.1
git push origin develop

# ลบ hotfix branch
git branch -d hotfix/1.0.1
```

### ข้อดีของ Git Flow

- โครงสร้างชัดเจน เข้าใจง่าย
- เหมาะกับโปรเจคที่มีรอบการ release ที่แน่นอน
- รองรับการทำงานพร้อมกันของหลายทีม
- มีเครื่องมือสนับสนุนมากมาย เช่น git-flow extension

### ข้อเสียของ Git Flow

- ซับซ้อนเกินไปสำหรับโปรเจคขนาดเล็ก
- การ merge บ่อยครั้งอาจทำให้เกิด conflict
- ไม่เหมาะกับการ deploy แบบต่อเนื่อง (continuous deployment)

## 🔄 GitHub Flow

GitHub Flow เป็น workflow ที่เรียบง่ายกว่า เหมาะสำหรับทีมที่ต้องการ deploy บ่อยๆ และมีระบบ CI/CD ที่ดี

### โครงสร้างของ GitHub Flow

GitHub Flow มี branch หลักเพียง 2 ประเภท:

1. **main/master**: branch หลักที่พร้อมสำหรับ production เสมอ
2. **feature/\***: branch สำหรับพัฒนาฟีเจอร์หรือแก้ไขบัค

### ขั้นตอนการทำงานใน GitHub Flow

1. **สร้าง branch**: แยก branch จาก main สำหรับฟีเจอร์หรือการแก้ไข
2. **พัฒนาและ commit**: ทำการพัฒนาและ commit บ่อยๆ
3. **Push และเปิด Pull Request**: push branch และเปิด PR เพื่อขอ review
4. **Review และแก้ไข**: ทีมทำการ review และแก้ไขตามคำแนะนำ
5. **Merge**: เมื่อผ่านการ review และการทดสอบแล้ว ให้ merge เข้า main
6. **Deploy**: deploy main branch ไปยัง production

```bash
# สร้าง feature branch
git checkout main
git pull
git checkout -b feature/new-login-page

# พัฒนาและ commit
git add .
git commit -m "Add new login page"
git push origin feature/new-login-page

# เปิด Pull Request บน GitHub
# หลังจาก review และแก้ไขเสร็จแล้ว

# merge เข้า main (ทำบน GitHub หรือ command line)
git checkout main
git merge feature/new-login-page
git push origin main

# ลบ feature branch
git branch -d feature/new-login-page
```

### ข้อดีของ GitHub Flow

- เรียบง่าย เข้าใจง่าย
- เหมาะกับการ deploy แบบต่อเนื่อง
- ลดความซับซ้อนในการจัดการ branch
- เน้นการใช้ Pull Request เพื่อการ review โค้ด

### ข้อเสียของ GitHub Flow

- ไม่มีแนวทางชัดเจนสำหรับการจัดการ release
- อาจไม่เหมาะกับโปรเจคที่ต้องสนับสนุนหลายเวอร์ชันพร้อมกัน
- ต้องมีระบบ CI/CD และการทดสอบที่ดี

## 🦊 GitLab Flow

GitLab Flow เป็นการผสมผสานแนวคิดของ Git Flow และ GitHub Flow เพื่อให้เหมาะกับการ deploy ที่มีหลายสภาพแวดล้อม (environments)

### โครงสร้างของ GitLab Flow

GitLab Flow มี branch หลักดังนี้:

1. **main/master**: โค้ดที่พร้อมสำหรับ production
2. **feature/\***: branch สำหรับพัฒนาฟีเจอร์
3. **environment branches**: เช่น staging, pre-production (optional)
4. **release branches**: สำหรับการจัดการเวอร์ชัน (optional)

### รูปแบบของ GitLab Flow

#### 1. Production branch with merge requests

```
feature → main → production
```

- **main**: branch หลักสำหรับการพัฒนา
- **production**: mirror ของระบบ production จริง
- ใช้ merge request เพื่อ merge จาก feature เข้า main
- deploy จาก main ไปยัง production และ merge เข้า production branch

#### 2. Environment branches

```
feature → main → staging → production
```

- เพิ่ม branch สำหรับแต่ละสภาพแวดล้อม
- การเปลี่ยนแปลงไหลจากซ้ายไปขวาเสมอ
- ช่วยให้ทดสอบในแต่ละสภาพแวดล้อมก่อน deploy จริง

#### 3. Release branches

```
feature → main → v1.0 → v1.1
```

- สร้าง branch สำหรับแต่ละเวอร์ชัน
- แก้ไขบัคโดยตรงบน release branch และ cherry-pick กลับไปยัง main
- เหมาะสำหรับซอฟต์แวร์ที่ต้องสนับสนุนหลายเวอร์ชัน

### ขั้นตอนการทำงานใน GitLab Flow

```bash
# สร้าง feature branch จาก main
git checkout main
git pull
git checkout -b feature/user-profile

# พัฒนาและ commit
git add .
git commit -m "Add user profile page"
git push origin feature/user-profile

# เปิด Merge Request บน GitLab
# หลังจาก review และแก้ไขเสร็จแล้ว merge เข้า main

# deploy ไปยัง staging
git checkout staging
git merge main
git push origin staging

# หลังจากทดสอบบน staging แล้ว deploy ไปยัง production
git checkout production
git merge staging
git push origin production
```

### ข้อดีของ GitLab Flow

- ยืดหยุ่นกว่า GitHub Flow แต่ไม่ซับซ้อนเท่า Git Flow
- รองรับการทำงานกับหลายสภาพแวดล้อม
- เหมาะกับการ deploy แบบต่อเนื่องและการจัดการ release
- มีแนวทางชัดเจนสำหรับการแก้ไขบัคใน production

### ข้อเสียของ GitLab Flow

- ซับซ้อนกว่า GitHub Flow
- ต้องมีความเข้าใจเรื่องการไหลของโค้ดระหว่าง branch
- อาจมีความซับซ้อนในการจัดการ merge conflict

## 🚀 Trunk-Based Development

Trunk-Based Development (TBD) เป็น workflow ที่เน้นการพัฒนาบน branch หลัก (trunk หรือ main) โดยตรง หรือใช้ short-lived feature branches ที่มีอายุไม่เกิน 1-2 วัน เหมาะสำหรับทีมที่ต้องการ deploy บ่อยมากและมีระบบ CI/CD ที่แข็งแกร่ง

### โครงสร้างของ Trunk-Based Development

TBD มีโครงสร้างที่เรียบง่ายมาก:

1. **main/trunk**: branch หลักที่ทุกคนพัฒนาร่วมกัน
2. **feature/\***: branch อายุสั้นสำหรับฟีเจอร์ที่ซับซ้อน (1-2 วัน)
3. **release/\***: branch สำหรับ release (optional)

### รูปแบบของ Trunk-Based Development

#### 1. Direct commits to trunk

```
       A---B---C---D  main/trunk
```

- นักพัฒนาทำการ commit โดยตรงไปยัง main
- เหมาะสำหรับการเปลี่ยนแปลงเล็กๆ น้อยๆ
- ต้องมีการทดสอบอัตโนมัติที่ครอบคลุม

#### 2. Short-lived feature branches

```
       A---B---C---F  main/trunk
           \     /
            D---E    feature
```

- สร้าง branch สำหรับฟีเจอร์ที่ซับซ้อน
- merge กลับเข้า main ภายใน 1-2 วัน
- ไม่ควรมี feature branch หลายอันพร้อมกัน

#### 3. Release branches (optional)

```
       A---B---C---D---E  main/trunk
               \
                R1---R2   release/1.0
```

- สร้าง release branch เมื่อต้องการ freeze โค้ดสำหรับ release
- แก้ไขบัคบน release branch และ cherry-pick กลับไปยัง main

### ขั้นตอนการทำงานใน Trunk-Based Development

```bash
# ดึงโค้ดล่าสุดจาก main
git checkout main
git pull

# การเปลี่ยนแปลงเล็กน้อย: commit โดยตรงไปยัง main
git add .
git commit -m "Fix typo in login page"
git push origin main

# การเปลี่ยนแปลงที่ซับซ้อน: สร้าง feature branch อายุสั้น
git checkout -b feature/search-optimization
git add .
git commit -m "Optimize search algorithm"

# ดึงการเปลี่ยนแปลงล่าสุดจาก main และ rebase
git checkout main
git pull
git checkout feature/search-optimization
git rebase main

# แก้ไข conflict (ถ้ามี) และ push
git push origin feature/search-optimization

# เปิด Pull Request และ merge เข้า main โดยเร็ว
```

### เทคนิคสำคัญใน Trunk-Based Development

#### Feature Flags

```java
if (featureFlags.isEnabled("new-search-algorithm")) {
    // โค้ดสำหรับฟีเจอร์ใหม่
} else {
    // โค้ดเดิม
}
```

- ใช้ feature flags เพื่อซ่อนฟีเจอร์ที่ยังพัฒนาไม่เสร็จ
- ช่วยให้สามารถ merge โค้ดเข้า main ได้เร็วขึ้น
- เปิดใช้งานฟีเจอร์เมื่อพร้อม

#### การทดสอบอัตโนมัติ

- ต้องมีการทดสอบอัตโนมัติที่ครอบคลุมและรวดเร็ว
- ใช้ CI/CD เพื่อทดสอบทุก commit
- ใช้ code review เพื่อตรวจสอบคุณภาพโค้ด

### ข้อดีของ Trunk-Based Development

- เรียบง่าย ลดความซับซ้อนในการจัดการ branch
- ลดปัญหา merge conflict ขนาดใหญ่
- เหมาะกับการ deploy แบบต่อเนื่อง (CI/CD)
- ช่วยให้ทีมทำงานร่วมกันได้อย่างรวดเร็ว

### ข้อเสียของ Trunk-Based Development

- ต้องมีวินัยในการเขียนโค้ดและการทดสอบสูง
- ต้องมีระบบ CI/CD และการทดสอบอัตโนมัติที่แข็งแกร่ง
- อาจไม่เหมาะกับทีมขนาดใหญ่ที่ยังไม่มีประสบการณ์
- ต้องใช้เทคนิคเพิ่มเติม เช่น feature flags

## 🔍 การเลือก Git Workflow ที่เหมาะสม

การเลือก Git Workflow ที่เหมาะสมขึ้นอยู่กับปัจจัยหลายอย่าง:

### คำถามที่ควรถามก่อนเลือก Workflow

1. **ขนาดของทีม**: ทีมมีขนาดเล็กหรือใหญ่?
2. **ความถี่ในการ deploy**: ต้องการ deploy บ่อยแค่ไหน?
3. **ประสบการณ์ของทีม**: ทีมมีความเชี่ยวชาญด้าน Git มากน้อยเพียงใด?
4. **ลักษณะของโปรเจค**: เป็นเว็บแอป, โมบายแอป, หรือซอฟต์แวร์ระบบ?
5. **การสนับสนุนหลายเวอร์ชัน**: ต้องสนับสนุนหลายเวอร์ชันพร้อมกันหรือไม่?
6. **ระบบ CI/CD**: มีระบบ CI/CD ที่แข็งแกร่งหรือไม่?

### คำแนะนำในการเลือก Workflow

| Workflow | เหมาะสำหรับ |
|----------|------------|
| **Git Flow** | - โปรเจคขนาดใหญ่<br>- ทีมที่มีรอบการ release ชัดเจน<br>- ซอฟต์แวร์ที่ต้องสนับสนุนหลายเวอร์ชัน |
| **GitHub Flow** | - ทีมขนาดเล็กถึงกลาง<br>- การ deploy แบบต่อเนื่อง<br>- เว็บแอปและบริการออนไลน์ |
| **GitLab Flow** | - ทีมที่ต้องการความยืดหยุ่น<br>- โปรเจคที่มีหลายสภาพแวดล้อม<br>- ต้องการจัดการ release อย่างเป็นระบบ |
| **Trunk-Based Development** | - ทีมที่มีประสบการณ์สูง<br>- ต้องการ deploy หลายครั้งต่อวัน<br>- มีระบบ CI/CD และการทดสอบที่แข็งแกร่ง |

## 📊 เปรียบเทียบ Git Workflows

| คุณลักษณะ | Git Flow | GitHub Flow | GitLab Flow | Trunk-Based |
|-----------|----------|-------------|-------------|-------------|
| ความซับซ้อน | สูง | ต่ำ | ปานกลาง | ต่ำ |
| จำนวน branch | มาก | น้อย | ปานกลาง | น้อยมาก |
| เหมาะกับการ deploy | รอบการ release | ต่อเนื่อง | ทั้งสองแบบ | ต่อเนื่อง |
| การจัดการ release | มีโครงสร้างชัดเจน | ไม่มีโครงสร้างเฉพาะ | ยืดหยุ่น | ใช้ tags หรือ release branches |
| การแก้ไขบัค | hotfix branches | feature branches | environment branches | trunk หรือ release branches |
| ความเสี่ยงของ conflict | สูง | ปานกลาง | ปานกลาง | ต่ำ |
| ความต้องการด้าน CI/CD | ปานกลาง | สูง | สูง | สูงมาก |

## 🛠️ เครื่องมือสนับสนุน Git Workflows

### Git Flow

```bash
# ติดตั้ง git-flow extension
# macOS
brew install git-flow

# Linux
apt-get install git-flow

# เริ่มต้นใช้งาน git-flow
git flow init

# สร้าง feature
git flow feature start user-authentication

# จบ feature
git flow feature finish user-authentication

# สร้าง release
git flow release start 1.0.0

# จบ release
git flow release finish 1.0.0
```

### GitHub Flow และ GitLab Flow

- GitHub และ GitLab มีเครื่องมือบนเว็บไซต์สำหรับจัดการ Pull/Merge Requests
- ใช้ Protected Branches เพื่อป้องกัน main branch
- ใช้ CI/CD pipelines เพื่อทดสอบและ deploy อัตโนมัติ

### Trunk-Based Development

- ใช้ Feature Flag Management Systems เช่น LaunchDarkly, Flagsmith
- ใช้ Pre-commit hooks เพื่อตรวจสอบโค้ดก่อน commit
- ใช้ Branch policies เพื่อบังคับใช้กฎการ review โค้ด

## 📚 แหล่งเรียนรู้เพิ่มเติม

- [Git Flow - Vincent Driessen](https://nvie.com/posts/a-successful-git-branching-model/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [GitLab Flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html)
- [Trunk-Based Development](https://trunkbaseddevelopment.com/)
- [Atlassian Git Workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)
