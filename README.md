M.K
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

  <footer>
    <p class="signature-title">Coded by</p>
    <p class="signature">M.Khasroof</p>
  </footer>

  <script>
    // التشفير باستخدام كلمة مرور قوية
    function encrypt() {
        const text = document.getElementById("input").value;
        const password = document.getElementById("password").value;
        if (!password || !text) {
            showMessage("يرجى إدخال النص وكلمة المرور!");
            return;
        }

        // استخدام PBKDF2 لتوليد مفتاح قوي من كلمة المرور
        const key = CryptoJS.PBKDF2(password, CryptoJS.enc.Hex.parse("1234567890abcdef"), { keySize: 256 / 32 });
        
        // تشفير النص باستخدام المفتاح الناتج
        const ciphertext = CryptoJS.AES.encrypt(text, key).toString();
        document.getElementById("output").value = ciphertext;
        showMessage("تم التشفير بنجاح!");
    }

    // فك التشفير باستخدام نفس كلمة المرور
    function decrypt() {
        const code = document.getElementById("input").value;
        const password = document.getElementById("password").value;
        try {
            if (!password || !code) {
                showMessage("يرجى إدخال النص وكلمة المرور!");
                return;
            }

            // استخدام PBKDF2 لتوليد نفس المفتاح لفك التشفير
            const key = CryptoJS.PBKDF2(password, CryptoJS.enc.Hex.parse("1234567890abcdef"), { keySize: 256 / 32 });

            // فك التشفير باستخدام نفس المفتاح
            const bytes = CryptoJS.AES.decrypt(code, key);
            const originalText = bytes.toString(CryptoJS.enc.Utf8);

            if (!originalText) throw new Error();
            document.getElementById("output").value = originalText;
            showMessage("تم فك التشفير بنجاح!");
        } catch {
            showMessage("فشل فك التشفير! تأكد من النص وكلمة المرور.");
        }
    }

    // نسخ النص المشفر إلى الحافظة
    function copyResult() {
        const output = document.getElementById("output");
        output.select();
        document.execCommand("copy");
        showMessage("تم النسخ!");
    }

    // مشاركة النص عبر WhatsApp
    function shareWhatsApp() {
        const text = document.getElementById("output").value;
        if (!text) {
            alert("لا يوجد نص للمشاركة!");
            return;
        }
        const url = "https://wa.me/?text=" + encodeURIComponent(text);
        window.open(url, "_blank");
    }

    // عرض الرسائل للمستخدم
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
