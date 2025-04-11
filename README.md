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
      width: 100%;
      padding: 15px;
      font-size: 18px;
      margin-bottom: 20px;
      border-radius: 12px;
      border: 2px solid #00FF00;
      resize: vertical;
      box-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
    }

    .input-wrapper {
      position: relative;
      width: 90%;
      max-width: 600px;
    }

    .toggle-eye {
      position: absolute;
      right: 15px;
      top: 50%;
      transform: translateY(-50%);
      cursor: pointer;
      color: #00FF00;
      font-size: 20px;
      user-select: none;
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

  <!-- كلمة مرور الدخول -->
  <div class="input-wrapper">
    <input id="sitePass" type="password" placeholder="ادخل كلمة سر الدخول" onkeyup="checkAccess(event)">
    <span class="toggle-eye" onclick="togglePassword('sitePass', this)">👁</span>
  </div>

  <!-- المحتوى الرئيسي -->
  <div id="mainArea" style="display:none">  
    <!-- كلمة المرور السرية للتشفير -->
    <div class="input-wrapper">
      <input id="password" type="password" placeholder="كلمة المرور السرّية" />
      <span class="toggle-eye" onclick="togglePassword('password', this)">👁</span>
    </div>

    <textarea id="input" placeholder="اكتب النص هنا..."></textarea>

    <div class="action-buttons">  
      <button onclick="encrypt();">تشفير النص</button>  
      <button onclick="decrypt();">فك التشفير</button>  
      <button onclick="copyResult();">نسخ</button>  
      <button onclick="pasteClipboard();">لصق من الحافظة</button>  
      <button onclick="shareWhatsApp();">مشاركة واتساب</button>  
      <button onclick="clearAll();">مسح الكل</button>  
    </div>

    <h3>النتيجة:</h3>  
    <textarea id="output" readonly></textarea>    
  </div>  

  <div id="statusMessage"></div>  

  <footer>  
    <p class="signature-title">Coded by</p>  
    <p class="signature">M.Khasroof</p>  
  </footer>  

  <script>  
    const sitePassword = "Khasroof09";  

    function checkAccess(e) {  
      if (e.key === "Enter") {  
        const input = document.getElementById("sitePass").value;  
        if (input === sitePassword) {  
          document.getElementById("sitePass").parentElement.style.display = 'none';  
          document.getElementById("mainArea").style.display = 'block';  
          showMessage("تم الدخول بنجاح!");  
        } else {  
          showMessage("كلمة المرور غير صحيحة!", true);  
        }  
      }  
    }  

    function togglePassword(inputId, el) {
      const input = document.getElementById(inputId);
      if (input.type === "password") {
        input.type = "text";
        el.textContent = "🙈";
      } else {
        input.type = "password";
        el.textContent = "👁";
      }
    }

    function encrypt() {  
      const text = document.getElementById("input").value;  
      const password = document.getElementById("password").value;  
      if (!password || !text) return showMessage("يرجى إدخال النص وكلمة المرور!", true);  
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
        showMessage("فشل فك التشفير! تأكد من النص وكلمة المرور.", true);  
      }  
    }  

    function copyResult() {  
      const output = document.getElementById("output");  
      output.select();  
      document.execCommand("copy");  
      showMessage("تم النسخ!");  
    }  

    async function pasteClipboard() {  
      try {  
        const text = await navigator.clipboard.readText();  
        document.getElementById("input").value = text;  
        showMessage("تم اللصق من الحافظة!");  
      } catch {  
        showMessage("لم يتمكن من الوصول للحافظة!", true);  
      }  
    }  

    function shareWhatsApp() {  
      const text = document.getElementById("output").value;  
      if (!text) return alert("لا يوجد نص للمشاركة!");  
      const url = "https://wa.me/?text=" + encodeURIComponent(text);  
      window.open(url, "_blank");  
    }  

    function clearAll() {  
      document.getElementById("input").value = '';  
      document.getElementById("output").value = '';  
      document.getElementById("password").value = '';  
      showMessage("تم المسح.");  
    }  

    function showMessage(message, isError = false) {  
      const status = document.getElementById("statusMessage");  
      status.textContent = message;  
      status.style.color = isError ? "#FF0000" : "#00FF00";  
      setTimeout(() => status.textContent = '', 4000);  
    }
  </script>
</body>
</html>
