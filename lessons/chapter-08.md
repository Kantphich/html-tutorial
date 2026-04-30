# บทที่ 8: แนวทางปฏิบัติที่ดีในการเตรียมหน้าเว็บให้พร้อมเทส (Testing Readiness)

มาถึงบทสุดท้ายและสำคัญที่สุดสำหรับเป้าหมายหลักของเราครับ: **การเขียน HTML ที่เป็นมิตรกับการทำ Automated Test ด้วย Cypress**

หลายคนเวลาเขียนเว็บจะมุ่งเน้นไปที่ความสวยงามจนลืมไปว่า ในระดับองค์กร เราต้องมีการรันเทสระบบอัตโนมัติ (Automated Testing) อยู่เสมอ และหากเราเขียนโค้ด HTML ไม่ดี คนเขียนเทส (QA/Tester) จะปวดหัวมาก!

---

## 1. ทำไมถึงไม่ควรใช้ CSS Class อ้างอิงเพื่อการเทส?

เวลาที่เราเขียน Cypress เราต้องบอกสคริปต์ว่า "ไปหาปุ่มตกลง แล้วคลิกซะ"
โดยปกติเราอาจจะใช้ Class เป็นตัวหา ดังนี้:

`cy.get('.btn-submit').click()`

**ปัญหาคือ:** วันดีคืนดี ดีไซเนอร์บอกว่าอยากเปลี่ยนปุ่มจากสีเขียวเป็นสีแดง โปรแกรมเมอร์เลยเข้าไปแก้โค้ด HTML เปลี่ยนชื่อคลาสเป็น `.btn-danger` แทน
**ผลลัพธ์:** CSS สวยงามตามสั่ง แต่... **เทสพังทันที! (Test Failed)** เพราะหาคลาส `.btn-submit` ไม่เจอแล้ว!

## 2. ทางออกคือ "Custom Data Attributes"

เพื่อแยก "ความสวยงาม" ออกจาก "การเทส" อย่างเด็ดขาด แนวทางปฏิบัติที่ดีที่สุด (Best Practice) ระดับโลกที่แนะนำโดยผู้สร้าง Cypress คือ:

**"ให้เพิ่ม Attribute พิเศษที่ขึ้นต้นด้วย `data-` สำหรับให้ Tester ใช้โดยเฉพาะ"**
(นิยมใช้ชื่อว่า `data-test`, `data-testid`, หรือ `data-cy`)

โปรแกรมเมอร์จะแก้ชื่อ Class, ย้าย Layout เปลี่ยนตาราง ยังไงก็ได้ ตราบใดที่ยังคงติดป้าย `data-test` ไว้ที่ชิ้นส่วนนั้นๆ เทสของเราก็จะไม่พัง!

---

## Step-by-step: สร้างหน้า Login ที่สมบูรณ์แบบเพื่อการเทส

เปิดไฟล์ `index.html` แล้วพิมพ์โค้ดชุดนี้ลงไป นี่คือโค้ดแบบอย่าง (Best Practice) ของหน้า Login ที่พร้อมให้ Cypress จัดการได้เลย!

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>เข้าสู่ระบบ</title>
    <style>
      /* ตกแต่งเล็กน้อย */
      body {
        font-family: Arial, sans-serif;
        display: flex;
        justify-content: center;
        margin-top: 50px;
      }
      .login-box {
        border: 1px solid #ccc;
        padding: 20px;
        border-radius: 8px;
        width: 300px;
      }
      .form-group {
        margin-bottom: 15px;
      }
      label {
        display: block;
        margin-bottom: 5px;
      }
      input[type="text"],
      input[type="password"] {
        width: 90%;
        padding: 8px;
      }
      .btn-primary {
        background-color: blue;
        color: white;
        padding: 10px 15px;
        border: none;
        width: 100%;
        cursor: pointer;
      }
    </style>
  </head>
  <body>
    <div class="login-box">
      <!-- เราติด data-test ไว้ที่หัวข้อเพื่อเอาไว้เช็คว่าเปิดมาถูกหน้า -->
      <h2 data-test="page-heading">เข้าสู่ระบบ</h2>

      <form action="/login-endpoint">
        <div class="form-group">
          <label for="username">ชื่อผู้ใช้งาน</label>
          <!-- ใส่ data-test ตรงจุดรับ input -->
          <input
            type="text"
            id="username"
            name="username"
            class="input-field"
            data-test="login-username-input"
          />
        </div>

        <div class="form-group">
          <label for="password">รหัสผ่าน</label>
          <input
            type="password"
            id="password"
            name="password"
            class="input-field"
            data-test="login-password-input"
          />
        </div>

        <!-- ใส่ data-test ตรงปุ่มกด -->
        <button type="submit" class="btn-primary" data-test="login-submit-btn">
          ล็อคอิน
        </button>
      </form>
    </div>
  </body>
</html>
```

### ทำไมโค้ดชุดนี้ถึงยอดเยี่ยม?

1. มี `<label>` ผูกกับ `id` ครบถ้วน (ผู้พิการใช้งานได้ และ Cypress มองเห็นชัด)
2. มี `name` ครบถ้วน สำหรับเตรียมส่งข้อมูลขึ้นเซิร์ฟเวอร์
3. **ที่สำคัญที่สุด:** มี `data-test` ในทุกๆ จุดที่คาดว่า Tester ต้องสั่งโปรแกรมให้มากดหรือมาพิมพ์

### และนี่คือสิ่งที่ QA / Tester จะเขียนใน Cypress:

```javascript
describe("ระบบ Login", () => {
  it("ผู้ใช้งานต้องสามารถล็อกอินเข้าสู่ระบบได้", () => {
    // 1. ไปที่หน้า Login
    cy.visit("/login");

    // 2. ตรวจสอบว่าเปิดถูกหน้า
    cy.get('[data-test="page-heading"]').should("contain", "เข้าสู่ระบบ");

    // 3. พิมพ์ข้อมูลผู้ใช้
    cy.get('[data-test="login-username-input"]').type("johndoe");
    cy.get('[data-test="login-password-input"]').type("P@ssw0rd123");

    // 4. กดปุ่มล็อคอิน
    cy.get('[data-test="login-submit-btn"]').click();

    // 5. คาดหวังว่าจะเจอหน้า Dashboard เป็นต้น...
  });
});
```

---

## สรุปบทเรียนทั้งหมด (Course Conclusion)

ขอแสดงความยินดีด้วยครับ! คุณเรียนจบหลักสูตรพื้นฐาน HTML & CSS แล้ว

คุณไม่ได้แค่เรียนรู้วิธีสร้างเว็บเหมือนคนทั่วไป แต่คุณเรียนรู้วิธีสร้างโครงสร้างหน้าเว็บ **แบบมืออาชีพ** ที่คิดเผื่อถึงการทำ Automated Test ในอนาคต ซึ่งเป็นสกิลที่เป็นที่ต้องการสูงมากในปัจจุบัน

**ทบทวนสิ่งที่คุณได้เรียนไป:**

1. โครงสร้าง HTML พื้นฐาน `<html>`, `<head>`, `<body>`
2. การจัดการเนื้อหาหัวข้อ `<h1>` และย่อหน้า `<p>`
3. การเพิ่มลิงก์ `<a>` และภาพ `<img>` (ห้ามลืม `alt`!)
4. การสร้างตารางและฟอร์มต่างๆ `<input>`, `<button>`
5. การแบ่งสัดส่วนเนื้อหาให้เป็นระบบ `<header>`, `<main>`, `<footer>`
6. การตกแต่งเว็บด้วย `CSS` เบื้องต้น
7. **หัวใจสำคัญ:** การใช้ Custom Data Attributes (`data-test`) เพื่อทำ Cypress Testing!

> **ไปลองฝึกฝนด้วยตัวเองเลย! สร้างหน้าเว็บของตัวเองสัก 2-3 หน้า รับรองว่าคุณจะเก่งขึ้นแน่นอน!**
