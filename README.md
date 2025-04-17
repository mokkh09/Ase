<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <title>آلة حاسبة M.K</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&display=swap');

    body {
      font-family: 'Share Tech Mono', monospace;
      background-color: #1A1A1D;
      color: #F1F1F1;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      margin: 0;
    }

    .calculator, #mKInterface {
      background: #000;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 20px #0f0;
      display: flex;
      flex-direction: column;
      align-items: center;
      width: 90%;
      max-width: 600px;
    }

    #display {
      width: 100%;
      height: 50px;
      margin-bottom: 10px;
      font-size: 24px;
      text-align: right;
      padding: 10px;
      background: #000;
      color: #0f0;
      border: none;
      outline: none;
    }

    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 60px);
      gap: 10px;
    }

    button {
      font-size: 20px;
      padding: 15px;
      background: #111;
      border: 1px solid #0f0;
      border-radius: 5px;
      color: #0f0;
      cursor: pointer;
    }

    button:hover {
      background: #0f0;
      color: #000;
    }

    #mKInterface {
      display: none;
      text-align: center;
    }

    textarea, input[type="password"] {
      background-color: #111;
      color: #00FF00;
      width: 90%;
      padding: 15px;
      font-size: 18px;
      margin: 10px 0;
      border-radius: 12px;
      border: 2px solid #00FF00;
      resize: vertical;
      box-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
    }

    .action-buttons button {
      margin: 5px;
    }

    .signature {
      font-size: 22px;
      color: #00FF00;
      text-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
      animation: glow 2s infinite alternate;
    }

    @keyframes glow {
      from { text-shadow: 0 0 5px #00FF00; }
      to { text-shadow: 0 0 15px #00FF00; }
    }

    #statusMessage {
      color: red;
      font-weight: bold;
      margin-top: 10px;
    }
  </style>
</head>
<body>

  <div class="calculator" id="calculator">
    <input type="text" id="display" readonly />
    <div class="buttons">
      <button onclick="clearDisplay()">C</button>
      <button onclick="appendValue('(')">(</button>
      <button onclick="appendValue(')')">)</button>
      <button onclick="appendValue('/')">÷</button>

      <button onclick="appendValue('7')">7</button>
      <button onclick="appendValue('8')">8</button>
      <button onclick="appendValue('9')">9</button>
      <button onclick="appendValue('*')">×</button>

      <button onclick="appendValue('4')">4</button>
      <button onclick="appendValue('5')">5</button>
      <button onclick="appendValue('6')">6</button>
      <button onclick="appendValue('-')">−</button>

      <button onclick="appendValue('1')">1</button>
      <button onclick="appendValue('2')">2</button>
      <button onclick="appendValue('3')">3</button>
      <button onclick="appendValue('+')">+</button>

      <button onclick="appendValue('0')">0</button>
      <button onclick="appendValue('.')">.</button>
      <button onclick="calculate()">=</button>
    </div>
  </div>

  <div id="mKInterface">
    <h2>تشفير النص | AES</h2>
    <input id="sitePass" type="password" placeholder="ادخل كلمة سر الدخول" onkeyup="checkAccess(event)">
    <div id="mainArea" style="display:none">
      <input id="password" type="password" placeholder="كلمة المرور السرّية" />
      <textarea id="input" placeholder="اكتب النص هنا..."></textarea>
      <div class="action-buttons">
        <button onclick="encrypt()">تشفير النص</button>
        <button onclick="decrypt()">فك التشفير</button>
        <button onclick="copyResult()">نسخ</button>
        <button onclick="pasteClipboard()">لصق من الحافظة</button>
        <button onclick="shareWhatsApp()">مشاركة واتساب</button>
        <button onclick="clearAll()">مسح الكل</button>
      </div>
      <h3>النتيجة:</h3>
      <textarea id="output" readonly></textarea>
    </div>
    <div id="statusMessage"></div>
    <footer>
      <p class="signature">M.Khasroof</p>
    </footer>
  </div>

  <script>
    function appendValue(value) {
      document.getElementById('display').value += value;
    }

    function clearDisplay() {
      document.getElementById('display').value = '';
    }

    function calculate() {
      const input = document.getElementById('display').value;

      if (input === '99+00') {
        document.getElementById('calculator').style.display = 'none';
        document.getElementById('mKInterface').style.display = 'flex';
        return;
      }

      try {
        const result = eval(input);
        document.getElementById('display').value = result;
      } catch {
        document.getElementById('display').value = 'خطأ';
      }
    }

    // واجهة M.K
    const sitePassword = "Kh.09";

    function checkAccess(e) {
      if (e.key === "Enter") {
        const input = document.getElementById("sitePass").value;
        if (input === sitePassword) {
          document.getElementById("sitePass").style.display = 'none';
          document.getElementById("mainArea").style.display = 'block';
          showMessage("تم الدخول بنجاح!");
        } else {
          showMessage("كلمة المرور غير صحيحة!");
        }
      }
    }

    function encrypt() {
      const text = document.getElementById("input").value;
      const password = document.getElementById("password").value;
      if (!password || !text) return showMessage("يرجى إدخال النص وكلمة المرور!");
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

    async function pasteClipboard() {
      try {
        const text = await navigator.clipboard.readText();
        document.getElementById("input").value = text;
        showMessage("تم اللصق من الحافظة!");
      } catch {
        showMessage("لم يتمكن من الوصول للحافظة!");
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

    function showMessage(message) {
      const status = document.getElementById("statusMessage");
      status.textContent = message;
      setTimeout(() => status.textContent = '', 4000);
    }
  </script>
</body>
</html>
