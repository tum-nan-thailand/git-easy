# แบบฝึกหัด: การใช้งาน Git Reset

## สถานการณ์

คุณกำลังพัฒนาเว็บไซต์ e-commerce และได้ทำการ commit การเปลี่ยนแปลงหลายอย่าง แต่พบว่ามีบางส่วนที่ยังไม่พร้อมสำหรับการ release หรือมีข้อผิดพลาดที่ต้องแก้ไข คุณต้องการใช้ Git Reset เพื่อจัดการกับประวัติ commit และการเปลี่ยนแปลงต่างๆ

### ขั้นตอนการจำลองสถานการณ์

1. สร้างโฟลเดอร์สำหรับโปรเจคและเริ่มต้น Git repository
```bash
mkdir reset-exercise
cd reset-exercise
git init
```

2. สร้างไฟล์ `index.html` ด้วยเนื้อหาเริ่มต้น
```bash
echo '<!DOCTYPE html>
<html>
<head>
  <title>E-Commerce Store</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Welcome to Our Store</h1>
    <nav>
      <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/products">Products</a></li>
        <li><a href="/cart">Cart</a></li>
      </ul>
    </nav>
  </header>
  <main>
    <section class="featured-products">
      <h2>Featured Products</h2>
      <!-- Products will be loaded here -->
    </section>
  </main>
  <footer>
    <p>&copy; 2023 E-Commerce Store</p>
  </footer>
  <script src="app.js"></script>
</body>
</html>' > index.html
```

3. สร้างไฟล์ `styles.css`
```bash
echo 'body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

header {
  background-color: #333;
  color: white;
  padding: 1rem;
}

nav ul {
  display: flex;
  list-style: none;
  padding: 0;
}

nav ul li {
  margin-right: 1rem;
}

nav ul li a {
  color: white;
  text-decoration: none;
}

.featured-products {
  padding: 2rem;
}

footer {
  background-color: #333;
  color: white;
  padding: 1rem;
  text-align: center;
}' > styles.css
```

4. Commit ไฟล์เริ่มต้น
```bash
git add index.html styles.css
git commit -m "Initial commit with basic HTML and CSS"
```

5. สร้างไฟล์ `app.js`
```bash
echo 'document.addEventListener("DOMContentLoaded", function() {
  // Load featured products
  loadFeaturedProducts();
});

function loadFeaturedProducts() {
  // Simulate API call
  const products = [
    { id: 1, name: "Product 1", price: 19.99 },
    { id: 2, name: "Product 2", price: 29.99 },
    { id: 3, name: "Product 3", price: 39.99 }
  ];
  
  const featuredProductsSection = document.querySelector(".featured-products");
  
  products.forEach(product => {
    const productElement = document.createElement("div");
    productElement.classList.add("product");
    productElement.innerHTML = `
      <h3>${product.name}</h3>
      <p>$${product.price}</p>
      <button>Add to Cart</button>
    `;
    featuredProductsSection.appendChild(productElement);
  });
}' > app.js
```

6. Commit ไฟล์ใหม่
```bash
git add app.js
git commit -m "Add JavaScript for loading featured products"
```

7. แก้ไขไฟล์ `styles.css` เพื่อเพิ่มสไตล์สำหรับสินค้า
```bash
echo 'body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

header {
  background-color: #333;
  color: white;
  padding: 1rem;
}

nav ul {
  display: flex;
  list-style: none;
  padding: 0;
}

nav ul li {
  margin-right: 1rem;
}

nav ul li a {
  color: white;
  text-decoration: none;
}

.featured-products {
  padding: 2rem;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 2rem;
}

.product {
  border: 1px solid #ddd;
  padding: 1rem;
  border-radius: 5px;
  transition: transform 0.3s ease;
}

.product:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

.product button {
  background-color: #333;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  cursor: pointer;
  border-radius: 3px;
}

footer {
  background-color: #333;
  color: white;
  padding: 1rem;
  text-align: center;
}' > styles.css
```

8. Commit การเปลี่ยนแปลง
```bash
git add styles.css
git commit -m "Add styles for product cards"
```

9. แก้ไขไฟล์ `app.js` เพื่อเพิ่มฟังก์ชันตะกร้าสินค้า
```bash
echo 'document.addEventListener("DOMContentLoaded", function() {
  // Load featured products
  loadFeaturedProducts();
  
  // Initialize cart
  initializeCart();
});

function loadFeaturedProducts() {
  // Simulate API call
  const products = [
    { id: 1, name: "Product 1", price: 19.99 },
    { id: 2, name: "Product 2", price: 29.99 },
    { id: 3, name: "Product 3", price: 39.99 }
  ];
  
  const featuredProductsSection = document.querySelector(".featured-products");
  
  products.forEach(product => {
    const productElement = document.createElement("div");
    productElement.classList.add("product");
    productElement.innerHTML = `
      <h3>${product.name}</h3>
      <p>$${product.price}</p>
      <button data-id="${product.id}" data-name="${product.name}" data-price="${product.price}">Add to Cart</button>
    `;
    featuredProductsSection.appendChild(productElement);
    
    // Add event listener to the button
    const button = productElement.querySelector("button");
    button.addEventListener("click", addToCart);
  });
}

function initializeCart() {
  // Create cart in localStorage if it doesn\'t exist
  if (!localStorage.getItem("cart")) {
    localStorage.setItem("cart", JSON.stringify([]));
  }
  
  // Update cart count
  updateCartCount();
}

function addToCart(event) {
  const button = event.target;
  const id = parseInt(button.getAttribute("data-id"));
  const name = button.getAttribute("data-name");
  const price = parseFloat(button.getAttribute("data-price"));
  
  // Get current cart
  const cart = JSON.parse(localStorage.getItem("cart"));
  
  // Check if product is already in cart
  const existingProductIndex = cart.findIndex(item => item.id === id);
  
  if (existingProductIndex !== -1) {
    // Increment quantity
    cart[existingProductIndex].quantity += 1;
  } else {
    // Add new product to cart
    cart.push({
      id,
      name,
      price,
      quantity: 1
    });
  }
  
  // Save updated cart
  localStorage.setItem("cart", JSON.stringify(cart));
  
  // Update cart count
  updateCartCount();
  
  // Show notification
  showNotification(`${name} added to cart!`);
}

function updateCartCount() {
  const cart = JSON.parse(localStorage.getItem("cart"));
  const count = cart.reduce((total, item) => total + item.quantity, 0);
  
  // Update UI
  const cartLink = document.querySelector(\'nav a[href="/cart"]\');
  cartLink.textContent = `Cart (${count})`;
}

function showNotification(message) {
  // Create notification element
  const notification = document.createElement("div");
  notification.classList.add("notification");
  notification.textContent = message;
  
  // Add to body
  document.body.appendChild(notification);
  
  // Remove after 3 seconds
  setTimeout(() => {
    notification.remove();
  }, 3000);
}

// DEBUG: Log cart contents to console
console.log("Cart initialized:", JSON.parse(localStorage.getItem("cart")));' > app.js
```

10. Commit การเปลี่ยนแปลง
```bash
git add app.js
git commit -m "Add shopping cart functionality with localStorage"
```

11. แก้ไขไฟล์ `styles.css` เพื่อเพิ่มสไตล์สำหรับการแจ้งเตือน
```bash
echo 'body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

header {
  background-color: #333;
  color: white;
  padding: 1rem;
}

nav ul {
  display: flex;
  list-style: none;
  padding: 0;
}

nav ul li {
  margin-right: 1rem;
}

nav ul li a {
  color: white;
  text-decoration: none;
}

.featured-products {
  padding: 2rem;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 2rem;
}

.product {
  border: 1px solid #ddd;
  padding: 1rem;
  border-radius: 5px;
  transition: transform 0.3s ease;
}

.product:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

.product button {
  background-color: #333;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  cursor: pointer;
  border-radius: 3px;
}

.notification {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background-color: #333;
  color: white;
  padding: 1rem;
  border-radius: 5px;
  animation: fadeIn 0.3s, fadeOut 0.3s 2.7s;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes fadeOut {
  from { opacity: 1; }
  to { opacity: 0; }
}

footer {
  background-color: #333;
  color: white;
  padding: 1rem;
  text-align: center;
}

/* Secret admin styles that shouldn\'t be committed */
.admin-panel {
  background-color: red;
  color: white;
  padding: 1rem;
  margin-top: 2rem;
}' > styles.css
```

12. Commit การเปลี่ยนแปลง
```bash
git add styles.css
git commit -m "Add notification styles and admin panel styles"
```

## คำถาม

1. คุณพบว่าได้ commit สไตล์สำหรับ admin panel ที่ยังไม่ควรเปิดเผย คุณจะใช้คำสั่ง Git อะไรเพื่อแก้ไข commit ล่าสุดและลบส่วนของ admin panel ออก?

2. หลังจากแก้ไข commit ล่าสุดแล้ว คุณพบว่ามีข้อความ debug ที่ไม่ควรอยู่ใน production ในไฟล์ `app.js` คุณจะใช้คำสั่ง Git อะไรเพื่อแก้ไขไฟล์และ commit การเปลี่ยนแปลง?

3. คุณต้องการยกเลิก commit ที่เพิ่มฟังก์ชันตะกร้าสินค้าทั้งหมด แต่ยังคงเก็บการเปลี่ยนแปลงไว้ในเครื่อง คุณจะใช้คำสั่ง Git อะไร?

4. หลังจากทดสอบฟีเจอร์ตะกร้าสินค้า คุณพบว่ามีข้อผิดพลาดมากเกินไป และต้องการยกเลิกการเปลี่ยนแปลงทั้งหมดที่เกี่ยวข้องกับตะกร้าสินค้า รวมถึงลบการเปลี่ยนแปลงในเครื่องด้วย คุณจะใช้คำสั่ง Git อะไร?

5. คุณได้ทำการ stage การเปลี่ยนแปลงในไฟล์ `styles.css` แต่ยังไม่ได้ commit และพบว่าการเปลี่ยนแปลงบางส่วนยังไม่พร้อม คุณจะใช้คำสั่ง Git อะไรเพื่อยกเลิกการ stage ไฟล์นี้?

6. คุณต้องการย้อนกลับไปยังสถานะของโปรเจคหลังจาก commit แรก (HTML และ CSS พื้นฐาน) คุณจะใช้คำสั่ง Git อะไร?

## เฉลย

### 1. การแก้ไข commit ล่าสุดเพื่อลบสไตล์ admin panel

```bash
# แก้ไขไฟล์ styles.css เพื่อลบส่วนของ admin panel
# จากนั้นใช้คำสั่ง
git add styles.css
git commit --amend
```

หรือถ้าไม่ต้องการเปลี่ยนข้อความ commit:

```bash
git add styles.css
git commit --amend --no-edit
```

### 2. การแก้ไขไฟล์ app.js และ commit การเปลี่ยนแปลง

```bash
# แก้ไขไฟล์ app.js เพื่อลบข้อความ debug
# จากนั้นใช้คำสั่ง
git add app.js
git commit -m "Remove debug log statement"
```

### 3. การยกเลิก commit ที่เพิ่มฟังก์ชันตะกร้าสินค้าแต่เก็บการเปลี่ยนแปลงไว้

```bash
git reset HEAD~1
```

หรือถ้าต้องการระบุ commit เฉพาะ:

```bash
git reset <commit-hash>
```

### 4. การยกเลิกการเปลี่ยนแปลงทั้งหมดที่เกี่ยวข้องกับตะกร้าสินค้า

```bash
git reset --hard HEAD~1
```

หรือถ้าต้องการระบุ commit เฉพาะ:

```bash
git reset --hard <commit-hash>
```

### 5. การยกเลิกการ stage ไฟล์ styles.css

```bash
git reset styles.css
```

หรือถ้าต้องการยกเลิกการ stage ทั้งหมด:

```bash
git reset
```

### 6. การย้อนกลับไปยังสถานะหลัง commit แรก

```bash
# หาแฮชของ commit แรก
git log --oneline

# ย้อนกลับไปยัง commit นั้น
git reset --hard <first-commit-hash>
```

หรือถ้ารู้ว่ามีกี่ commit:

```bash
# สมมติว่ามี 4 commits ทั้งหมด และต้องการกลับไปที่ commit แรก
git reset --hard HEAD~3
```

## ข้อแนะนำเพิ่มเติม

1. **ใช้ `git reflog` เพื่อกู้คืน**: ถ้าคุณใช้ `git reset --hard` และต้องการกู้คืนการเปลี่ยนแปลง ให้ใช้ `git reflog` เพื่อดูประวัติการเปลี่ยนแปลง HEAD

```bash
git reflog
git checkout <commit-hash>
git checkout -b recovery-branch
```

2. **ใช้ `git reset --soft` เพื่อแก้ไข commit**: ถ้าคุณต้องการแก้ไขหลาย commits ล่าสุด ให้ใช้ `git reset --soft HEAD~n` เพื่อย้อนกลับ n commits แต่เก็บการเปลี่ยนแปลงไว้ใน staging area

3. **ใช้ `git stash` ก่อน reset**: ถ้าคุณไม่แน่ใจว่าต้องการเก็บการเปลี่ยนแปลงหรือไม่ ให้ใช้ `git stash` ก่อน

4. **ระวังการใช้ `--hard`**: การใช้ `--hard` จะลบการเปลี่ยนแปลงที่ยังไม่ได้ commit ทั้งหมด ควรใช้ด้วยความระมัดระวัง

## แบบฝึกหัดเพิ่มเติม

ลองทำตามขั้นตอนการจำลองสถานการณ์และทดลองใช้ Git Reset ประเภทต่างๆ:

1. ใช้ `git reset --soft` เพื่อย้อนกลับ 2 commits และรวมเป็น commit เดียว
2. ใช้ `git reset --mixed` เพื่อย้อนกลับ 1 commit และแก้ไขการเปลี่ยนแปลงก่อน commit ใหม่
3. ใช้ `git reset --hard` เพื่อย้อนกลับไปยัง commit เฉพาะ
4. ใช้ `git reflog` และ `git reset` เพื่อกู้คืนการเปลี่ยนแปลงหลังจากใช้ `git reset --hard`
