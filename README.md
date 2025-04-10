<!M.Khasroof!>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <title>تشفير النصوص | AES</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Share+Tech+Mono&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Share Tech Mono', monospace;
      background-color: #0d0d0d;
      background-image: radial-gradient(circle at center, #111 0%, #000 100%);
      color: #F1F1F1;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 30px;
      min-height: 100vh;
      justify-content: center;
      text-align: center;
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

    .chat-log {
      width: 90%;
      max-width: 600px;
      background-color: #111;
      border: 2px solid #00FF00;
      border-radius: 12px;
      padding: 15px;
      margin-top: 30px;
      box-shadow: 0 0 10px #00FF00;
      text-align: left;
      overflow-y: auto;
      max-height: 300px;
    }

    .chat-entry {
      background-color: #000;
      color: #00FF00;
      padding: 10px;
      margin: 5px 0;
      border-radius: 10px;
      border-left: 4px solid #00FF00;
    }

    #statusMessage {
      margin-top: 20px;
      color: #FF0000;
      font-size: 20px;
      font-weight: bold;
    }

    footer {
      margin-top: 40px;
      text-align: center;
    }

    .signature-title {
      font-family: 'Share Tech Mono', monospace;
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
  </style>
</head>
<body>

  <h2>تشفير النصوص | AES</h2>

  <input id="password" type="password" placeholder="كلمة المرور السرّية" />
  <textarea id="input" placeholder="اكتب النص هنا..."></textarea>

  <div class="action-buttons">
    <button onclick="encrypt()">تشفير النص</button>
    <button onclick="decrypt()">فك التشفير</button>
    <button onclick="copyResult()">نسخ</button>
    <button onclick="shareWhatsApp()">مشاركة واتساب</button>
  </div>

  <h3>النتيجة:</h3>
  <textarea id="output" readonly></textarea>

  <div id="statusMessage"></div>

  <div id="chatLog" class="chat-log"></div>

  <footer>
    <p class="signature-title">Coded by</p>
    <p class="signature">M.Khasroof</p>
  </footer>

  <script>
    function encrypt() {
      const text = document.getElementById("input").value;
      const password = document.getElementById("password").value;
      if (!text || !password) {
        showMessage("يرجى إدخال النص وكلمة المرور!");
        return;
      }
      const ciphertext = CryptoJS.AES.encrypt(text, password).toString();
      document.getElementById("output").value = ciphertext;
      showMessage("تم التشفير!");
      logUserMessage(text);
    }

    function decrypt() {
      const code = document.getElementById("input").value;
      const password = document.getElementById("password").value;
      try {
        const bytes = CryptoJS.AES.decrypt(code, password);
        const originalText = bytes.toString(CryptoJS.enc.Utf8);
        if (!originalText) throw new Error();
        document.getElementById("output").value = originalText;
        showMessage("تم فك التشفير!");
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

    function logUserMessage(text) {
      const chatLog = document.getElementById("chatLog");
      for (let i = 0; i < 3; i++) {
        const entry = document.createElement("div");
        entry.className = "chat-entry";
        entry.textContent = `مستخدم كتب: ${text}`;
        chatLog.appendChild(entry);
      }
    }

    function showMessage(msg) {
      const status = document.getElementById("statusMessage");
      status.textContent = msg;
      setTimeout(() => {
        status.textContent = "";
      }, 3000);
    }
  </script>

</body>
</html>
