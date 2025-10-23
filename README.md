<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>FLICK</title>

  <style>
    body {
      font-family: "Tajawal", sans-serif;
      background-color: #fff;
      color: #333;
      direction: rtl;
      text-align: center;
      margin: 0;
      padding: 0;
    }

    .app-container {
      max-width: 400px;
      margin: 30px auto;
      background: #ffffff;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 0 15px rgba(0,0,0,0.1);
    }

    .logo-container {
      background-color: #fd6c05;
      padding: 20px;
      border-radius: 15px;
      margin-bottom: 15px;
    }

    .app-logo {
      width: 120px;
      height: 120px;
      border-radius: 50%;
    }

    .app-title {
      color: #fd6c05;
      margin-bottom: 15px;
    }

    input, textarea, button {
      width: 90%;
      margin: 8px 0;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 8px;
      font-size: 16px;
    }

    button {
      background-color: #fd6c05;
      color: white;
      border: none;
      cursor: pointer;
      transition: 0.3s;
    }

    button:hover {
      background-color: #e85c00;
    }

    .hidden {
      display: none;
    }

    #ordersList {
      text-align: right;
      font-size: 14px;
      margin-top: 10px;
    }

    .order-card {
      border: 1px solid #ddd;
      padding: 10px;
      border-radius: 10px;
      margin-bottom: 10px;
      background-color: #fff5f0;
    }
  </style>
</head>

<body>

  <div class="app-container">
    <div class="logo-container">
      <img src="4.png" alt="FLICK Logo" class="app-logo">
    </div>

    <h1 class="app-title">FLICK</h1>

    <!-- تسجيل الدخول -->
    <div id="register-section">
      <h2>تسجيل الدخول</h2>
      <input type="text" id="userName" placeholder="الاسم">
      <input type="tel" id="userPhone" placeholder="رقم الهاتف">
      <button onclick="registerUser()">تسجيل</button>
    </div>

    <!-- نموذج الطلب -->
    <div id="order-section" class="hidden">
      <h2>إرسال طلب جديد</h2>

      <form id="orderForm">
        <h3>بيانات الراسل</h3>
        <input type="text" id="senderName" placeholder="اسم الراسل" required>
        <input type="text" id="senderAddress" placeholder="عنوان الراسل" required>
        <input type="tel" id="senderPhone" placeholder="رقم الراسل" required>

        <h3>بيانات المرسل إليه (اختياري)</h3>
        <input type="text" id="receiverName" placeholder="اسم المرسل إليه">
        <input type="text" id="receiverAddress" placeholder="عنوان المرسل إليه">
        <input type="tel" id="receiverPhone" placeholder="رقم المرسل إليه">

        <h3>تفاصيل الطلب</h3>
        <textarea id="packageDetails" placeholder="تفاصيل الطلب (اختياري)"></textarea>

        <label for="packageImage">رفع صورة (اختياري):</label>
        <input type="file" id="packageImage" accept="image/*">

        <button type="submit">إرسال الطلب</button>
      </form>
    </div>

    <!-- صفحة الإدارة -->
    <div id="admin-section" class="hidden">
      <h2>الطلبات المستلمة (للإدارة فقط)</h2>
      <div id="ordersList"></div>
    </div>

  </div>

  <script>
    function registerUser() {
      const name = document.getElementById('userName').value.trim();
      const phone = document.getElementById('userPhone').value.trim();

      if (!name || !phone) {
        alert("الرجاء إدخال الاسم ورقم الهاتف.");
        return;
      }

      localStorage.setItem('userName', name);
      localStorage.setItem('userPhone', phone);

      document.getElementById('register-section').classList.add('hidden');

      // لو المستخدم هو الأدمن (المالك)
      if (phone === "0000") {
        document.getElementById('admin-section').classList.remove('hidden');
        loadOrders();
      } else {
        document.getElementById('order-section').classList.remove('hidden');
      }
    }

    document.getElementById('orderForm').addEventListener('submit', function (e) {
      e.preventDefault();

      const order = {
        senderName: document.getElementById('senderName').value,
        senderAddress: document.getElementById('senderAddress').value,
        senderPhone: document.getElementById('senderPhone').value,
        receiverName: document.getElementById('receiverName').value || "غير محدد",
        receiverAddress: document.getElementById('receiverAddress').value || "غير محدد",
        receiverPhone: document.getElementById('receiverPhone').value || "غير محدد",
        packageDetails: document.getElementById('packageDetails').value || "لا توجد تفاصيل",
        image: document.getElementById('packageImage').files[0]
          ? document.getElementById('packageImage').files[0].name
          : "بدون صورة"
      };

      let orders = JSON.parse(localStorage.getItem('orders')) || [];
      orders.push(order);
      localStorage.setItem('orders', JSON.stringify(orders));

      alert("تم إرسال الطلب بنجاح ✅");
      document.getElementById('orderForm').reset();
    });

    function loadOrders() {
      const ordersList = document.getElementById('ordersList');
      ordersList.innerHTML = "";

      const orders = JSON.parse(localStorage.getItem('orders')) || [];

      if (orders.length === 0) {
        ordersList.innerHTML = "<p>لا توجد طلبات بعد.</p>";
        return;
      }

      orders.forEach((order, i) => {
        const div = document.createElement('div');
        div.classList.add('order-card');
        div.innerHTML = `
          <strong>طلب رقم ${i + 1}</strong><br>
          <b>الراسل:</b> ${order.senderName} (${order.senderPhone})<br>
          <b>العنوان:</b> ${order.senderAddress}<br>
          <b>المرسل إليه:</b> ${order.receiverName} (${order.receiverPhone})<br>
          <b>العنوان:</b> ${order.receiverAddress}<br>
          <b>التفاصيل:</b> ${order.packageDetails}<br>
          <b>الصورة:</b> ${order.image}
        `;
        ordersList.appendChild(div);
      });
    }
  </script>

</body>
</html>
