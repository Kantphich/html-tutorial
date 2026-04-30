# บทที่ 5: การสร้างฟอร์มรับข้อมูล (Forms & Inputs)

มาถึงหัวข้อที่สำคัญที่สุดเรื่องหนึ่งของ HTML และ **สำคัญที่สุดสำหรับการเขียน Cypress Test!** 
ฟอร์ม (Form) คือส่วนที่ใช้รับข้อมูลจากผู้ใช้ เช่น หน้าเข้าสู่ระบบ (Login), หน้าสมัครสมาชิก, หรือช่องค้นหาข้อมูล

---

## Step-by-step: การสร้างแบบฟอร์ม

ในบทนี้เราจะสร้างหน้า "แบบฟอร์มสมัครสมาชิก" อย่างง่ายกันครับ เปิดไฟล์ `index.html` แล้วเคลียร์โค้ดใน `<body>` ให้ว่างไว้รอเลย

### Step 1: แท็ก `<form>`
ทุกอย่างที่เป็นการรับข้อมูลเพื่อส่งไปยังเซิร์ฟเวอร์ จะต้องถูกคลุมด้วยแท็ก `<form>` เสมอ

```html
<body>
    <h2>แบบฟอร์มสมัครสมาชิก</h2>
    
    <!-- action คือ URL ปลายทางที่จะส่งข้อมูลไป (ตอนนี้เรายังไม่มีเซิร์ฟเวอร์ ใส่ # ไว้ก่อน) -->
    <form action="#">
        
        <!-- เราจะใส่ช่องรับข้อมูลตรงนี้ -->
        
    </form>
</body>
```

### Step 2: ช่องรับข้อมูลแบบข้อความ (Text & Password)
การรับข้อมูลจะใช้แท็ก `<input>` (ไม่มีแท็กปิด) โดยระบุชนิดข้อมูลผ่าน Attribute `type`

```html
    <form action="#">
        
        <p>ชื่อผู้ใช้ (Username):</p>
        <input type="text">
        
        <p>รหัสผ่าน (Password):</p>
        <input type="password">
        
    </form>
```
*ลองเซฟแล้วไปพิมพ์ดูครับ ช่อง password เวลาพิมพ์ข้อมูลจะกลายเป็นจุดดำๆ ทันที*

### Step 3: การใช้ Placeholder และ Value
- `placeholder="ข้อความ"`: คือข้อความจางๆ ที่ใบ้ให้ผู้ใช้รู้ว่าต้องกรอกอะไร
- `value="ข้อความ"`: คือค่าเริ่มต้นที่มีการกรอกรอไว้เลย (ถ้ามี)

ลองแก้ช่อง Username เป็นแบบนี้:
```html
        <input type="text" placeholder="กรอกชื่อผู้ใช้งานภาษาอังกฤษ">
```

### Step 4: แอตทริบิวต์ที่สำคัญที่สุดสำหรับ Cypress (`id` และ `name`)
เพื่อให้ Cypress ค้นหาช่องเหล่านี้เจอและจำลองการพิมพ์ (Type) ได้ เรา **ต้อง** ใส่ `id` หรือ `name` ให้ชัดเจน และไม่ซ้ำกัน

```html
    <form action="#">
        
        <p>ชื่อผู้ใช้ (Username):</p>
        <input type="text" id="username" name="username" placeholder="กรอกชื่อผู้ใช้งานภาษาอังกฤษ">
        
        <p>รหัสผ่าน (Password):</p>
        <input type="password" id="password" name="password" placeholder="กรอกรหัสผ่าน">
        
    </form>
```

### Step 5: จับคู่ข้อความกับช่องกรอกด้วย `<label>` (สำคัญเรื่อง Accessibility)
สังเกตว่าใน Step 4 เราใช้ `<p>` เพื่อบอกว่าช่องนั้นคืออะไร ซึ่ง **ไม่ถูกต้องตามหลักการที่ควรจะเป็น** 
เราควรใช้แท็ก `<label>` และผูกมันเข้ากับ `<input>` ด้วยแอตทริบิวต์ `for` (โดยค่าของ `for` ต้องตรงกับ `id` ของ `<input>`)

```html
    <!-- ลบโค้ดเดิมใน form ออก แล้วใช้โค้ดนี้แทน -->
    <form action="#">
        
        <!-- โยง for="username" เข้ากับ id="username" -->
        <label for="username">ชื่อผู้ใช้ (Username):</label>
        <input type="text" id="username" name="username" placeholder="กรอกชื่อผู้ใช้งาน">
        <br><br>
        
        <!-- โยง for="password" เข้ากับ id="password" -->
        <label for="password">รหัสผ่าน (Password):</label>
        <input type="password" id="password" name="password">
        <br><br>

    </form>
```
*ลองดูผลลัพธ์ครับ **จุดเด่นคือ:** เมื่อคุณ "คลิก" ที่ข้อความคำว่า "รหัสผ่าน (Password):" ตัวเคอร์เซอร์จะกระโดดไปโฟกัสที่ช่องใส่รหัสผ่านอัตโนมัติ! นี่คือประโยชน์ของ `<label>`*

### Step 6: ช่องรับข้อมูลประเภทอื่นๆ (Radio, Checkbox, Dropdown)
มาเพิ่มช่องรับข้อมูลที่น่าสนใจกันต่อครับ ใส่ต่อจาก Password ได้เลย

```html
        <!-- Radio Button (เลือกได้ข้อเดียว) ต้องตั้งชื่อ name ให้เหมือนกันมันถึงจะผูกกลุ่มกัน -->
        <label>เพศ:</label>
        <input type="radio" id="gender-male" name="gender" value="ชาย">
        <label for="gender-male">ชาย</label>
        
        <input type="radio" id="gender-female" name="gender" value="หญิง">
        <label for="gender-female">หญิง</label>
        <br><br>

        <!-- Checkbox (เลือกได้หลายข้อ) -->
        <input type="checkbox" id="accept-rules" name="accept">
        <label for="accept-rules">ฉันยอมรับเงื่อนไขการให้บริการ</label>
        <br><br>

        <!-- Dropdown (เลือกจากรายการ) -->
        <label for="country">ประเทศ:</label>
        <select id="country" name="country">
            <option value="th">ไทย</option>
            <option value="us">สหรัฐอเมริกา</option>
            <option value="jp">ญี่ปุ่น</option>
        </select>
        <br><br>
```

### Step 7: ปุ่มกด (Buttons)
ขาดไม่ได้คือปุ่มส่งข้อมูล เราสามารถสร้างปุ่มได้ 2 แบบ (เลือกใช้อย่างใดอย่างหนึ่ง):
1. `<input type="submit" value="ลงทะเบียน">`
2. `<button type="submit">ลงทะเบียน</button>` *(แบบนี้ยืดหยุ่นกว่า แนะนำให้ใช้!)*

```html
        <!-- ใส่ปุ่มไว้ท้ายสุดก่อนปิดแท็ก </form> -->
        <button type="submit" id="submit-btn" data-test="register-button">ลงทะเบียนเลย!</button>
```

---

## สรุปบทที่ 5 และเคล็ดลับวิชา Cypress

Cypress รัก Form มากครับ ตัวอย่างการเขียนเทส (ด้วย Javascript) ที่ Cypress จะเข้ามาจัดการกับฟอร์มของเรา:

```javascript
// ตัวอย่าง (แค่ให้ดูคอนเซปต์ ยังไม่ต้องเขียนตาม)
cy.get('#username').type('JohnDoe');         // พิมพ์ชื่อลงในช่อง username
cy.get('#password').type('123456');          // พิมพ์รหัสผ่าน
cy.get('#gender-male').click();              // เลือกเพศชาย
cy.get('#accept-rules').check();             // ติ๊กยอมรับเงื่อนไข
cy.get('#country').select('th');             // เลือกประเทศเป็นไทย
cy.get('[data-test="register-button"]').click(); // กดปุ่มลงทะเบียน
```

การที่เรามี `id`, `name`, หรือ `data-test` ชัดเจนในบทนี้ จะทำให้หน้าเว็บของเราพร้อมมากๆ สำหรับการเทส!

> ในบทที่ 6 เราจะมารู้จักกับ **Layout และการแบ่งสัดส่วนหน้าเว็บ (Semantic Tags)** เพื่อจัดระเบียบเนื้อหากันครับ
