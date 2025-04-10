<!M.K><html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>تشفير ودردشة | M.Khasroof</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&display=swap');body {
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

h2, h3 {
  font-size: 28px;
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
  from { text-shadow: 0 0 5px #00FF00, 0 0 10px #00FF00; }
  to { text-shadow: 0 0 15px #00FF00, 0 0 30px #00FF00; }
}

#publicChatBox {
  width: 90%;
  max-width: 600px;
  margin-top: 40px;
  background-color: #111;
  border: 2px solid #00FF00;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 0 10px #00FF00;
}

#publicChatLog {
  background-color: #000;
  color: #00FF00;
  height: 200px;
  overflow-y: auto;
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 8px;
  border: 1px solid #00FF00;
  text-align: right;
}

#publicMessage {
  width: calc(100% - 110px);
  display: inline-block;
  padding: 10px;
  background-color: #111;
  color: #00FF00;
  border: 2px solid #00FF00;
  border-radius: 8px;
  font-size: 16px;
}

#publicChatBox button {
  padding: 10px 20px;
  margin-left: 10px;
}

  </style>
</head>
<body>  <h2>تشفير النص | AES</h2>  <input id="password" type="password" placeholder="كلمة المرور السرّية" />
  <textarea id="input" placeholder="اكتب النص هنا..."></textarea>  <div>
    <button onclick="encrypt()">تشفير</button>
    <button onclick="decrypt()">فك التشفير</button>
    <button onclick="copyResult()">نسخ</button>
  </div>  <h3>النتيجة:</h3>
  <textarea id="output" readonly></textarea>
  <div id="statusMessage"></div>  <h3>الدردشة العامة</h3>
  <div id="publicChatBox">
    <div id="publicChatLog"></div>
    <input type="text" id="publicMessage" placeholder="اكتب رسالتك هنا..." />
    <button onclick="sendPublicMessage()">إرسال</button>
  </div>  <footer>
    <p class="signature-title">Coded by</p>
    <p class="signature">M.Khasroof</p>
  </footer>  <script>
    function encrypt() {
      const text = document.getElementById("input").value;
      const password = document.getElementById("password").value;
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

    function showMessage(message) {
      const status = document.getElementById("statusMessage");
      status.textContent = message;
      setTimeout(() => status.textContent = '', 3000);
    }

    function sendPublicMessage() {
      const input = document.getElementById("publicMessage");
      const msg = input.value.trim();
      if (!msg) return;
      const log = document.getElementById("publicChatLog");
      const line = document.createElement("div");
      line.textContent = `مستخدم: ${msg}`;
      log.appendChild(line);
      log.scrollTop = log.scrollHeight;
      input.value = "";
    }
  </script></body>
</html>
