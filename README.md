<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>تشفير النص | AES</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&display=swap');

    body {
      font-family: 'Share Tech Mono', monospace;
      background-color: #1A1A1D;
      color: #F1F1F1;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 30px;
      min-height: 100vh;
      justify-content: center;
      text-align: center;
      background-image: radial-gradient(circle at center, #111 0%, #000 100%);
    }

    h2 {
      font-size: 34px;
      color: #00FF00;
      text-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
      margin-bottom: 20px;
    }

    textarea, input {
      background-color: #111;
      color: #00FF00;
      width: 90%;
      max-width: 600px;
      padding: 15px;
      font-size: 18px;
      margin-bottom: 20px;
      border-radius: 12px;
      border: 2px solid #00FF00;
      resize: vertical;
      box-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
    }

    button {
      padding: 15px 30px;
      margin: 10px;
      border: 2px solid #00FF00;
      border-radius: 12px;
      background-color: #000;
      color: #00FF00;
      font-size: 18px;
      cursor: pointer;
      box-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
      transition: transform 0.2s, background-color 0.3s;
    }

    button:hover {
      background-color: #00FF00;
      color: #000;
      transform: scale(1.1);
    }

    #output {
      background-color: #111;
      color: #F1F1F1;
    }

    .action-buttons {
      margin-top: 20px;
    }

    footer {
      margin-top: 40px;
      text-align: center;
    }

    .signature-title {
      font-size: 18px;
      color: #00FF00;
    }

    .signature {
      font-size: 22px;
      color: #00FF00;
      text-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
      animation: glow 2s infinite alternate;
    }

    @keyframes glow {
      from {
        text-shadow: 0 0 5px #00FF00, 0 0 10px #00FF00;
      }
      to {
        text-shadow: 0 0 15px #00FF00, 0 0 30px #00FF00;
      }
    }

    #statusMessage {
      margin-top: 20px;
      color: #FF0000;
      font-size: 20px;
      font-weight: bold;
    }

    #logContainer {
      margin-top: 50px;
      background: #0f0f0f;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 0 10px #00FF00;
      max-width: 600px;
      width: 90%;
    }
  </style>
</head>
<body>

  <h2>تشفير النص | AES</h2>

  <input id="password" type="password" placeholder="كلمة المرور السرّية" />
  <textarea id="input" placeholder="اكتب النص هنا..."></textarea>

  <div class="action-buttons">
    <button onclick="encrypt();">تشفير النص</button>
    <button onclick="decrypt();">فك التشفير</button>
    <button onclick="copyResult();">نسخ</button>
    <button onclick="shareWhatsApp();">مشاركة واتساب</button>
  </div>

  <h3>النتيجة:</h3>
  <textarea id="output" readonly></textarea>

  <div id="statusMessage"></div>

  <div id="logContainer"></div>

  <footer>
    <p class="signature-title">Coded by</p>
    <p class="signature">M.Khasroof</p>
  </footer>

  <script>
    // تسجيل الدخول
    document.addEventListener("DOMContentLoaded", () => {
      const visits = JSON.parse(localStorage.getItem("visits") || "[]");
      const newVisit = {
        time: new Date().toLocaleString(),
        action: "دخل الموقع"
      };
      visits.push(newVisit);
      localStorage.setItem("visits", JSON.stringify(visits));
      renderLog();

      // إذا كان هناك نص مشترك من واتساب
      const urlParams = new URLSearchParams(window.location.search);
      const sharedText = urlParams.get('text');
      if (sharedText) {
        document.getElementById('input').value = decodeURIComponent(sharedText);
      }
    });

    // تسجيل الكتابة
    document.getElementById("input").addEventListener("input", () => {
      const visits = JSON.parse(localStorage.getItem("visits") || "[]");
      const text = document.getElementById("input").value;
      if (text.length > 0) {
        visits.push({
          time: new Date().toLocaleString(),
          action: "كتب: " + text.slice(0, 30) + "..."
        });
        localStorage.setItem("visits", JSON.stringify(visits));
        renderLog();
      }
    });

    function renderLog() {
      const logContainer = document.getElementById("logContainer");
      const visits = JSON.parse(localStorage.getItem("visits") || "[]");
      logContainer.innerHTML = "<h3>سجل النشاط:</h3>" + visits.slice(-10).reverse().map(v => `
        <p style="font-size: 14px; color: #00FF00;">[${v.time}] ${v.action}</p>
      `).join("");
    }

    function encrypt() {
      const text = document.getElementById("input").value;
      const password = document.getElementById("password").value;
      if (!password || !text) {
        showMessage("يرجى إدخال النص وكلمة المرور!");
        return;
      }
      const ciphertext = CryptoJS.AES.encrypt(text, password).toString();
      document.getElementById("output").value = ciphertext;
      showMessage("تم التشفير بنجاح!");
    }

    function decrypt() {
      const code = document.getElementById("input").value;
      const password = document.getElementById("password").value;
      try {
        const bytes = CryptoJS.AES.decrypt(code, password);
        const originalText = bytes.toString(CryptoJS.enc.Utf8);
        if (!originalText) throw new Error();
        document.getElementById("output").value = originalText;
        showMessage("تم فك التشفير بنجاح!");
      } catch {
        showMessage("فشل فك التشفير! تأكد من النص وكلمة المرور.");
      }
    }

    function copyResult() {
      const output = document.getElementById("output");
      output.select();
      document.execCommand("copy");
      showMessage("تم النسخ!");
    }

    function shareWhatsApp() {
      const text = document.getElementById("output").value;
      if (!text) {
        alert("لا يوجد نص للمشاركة!");
        return;
      }
      const url = "https://wa.me/?text=" + encodeURIComponent(text);
      window.open(url, "_blank");
    }

    function showMessage(message) {
      const statusMessage = document.getElementById("statusMessage");
      statusMessage.textContent = message;
      setTimeout(() => {
        statusMessage.textContent = '';
      }, 3000);
    }
  </script>
</body>
</html>
