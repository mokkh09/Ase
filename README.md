M.K
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <title>آلة حاسبة</title>
  <style>
    body {
      background-color: #000;
      color: #00ff00;
      font-family: 'Courier New', monospace;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
    }
    h1 {
      text-shadow: 0 0 10px #0f0;
    }
    .calculator, #mkInterface {
      border: 2px solid #0f0;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 0 20px #0f0;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    input[type="text"] {
      background: #111;
      color: #0f0;
      border: none;
      padding: 10px;
      font-size: 24px;
      width: 250px;
      margin-bottom: 10px;
      text-align: right;
      border-radius: 10px;
    }
    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 60px);
      gap: 10px;
    }
    button {
      padding: 15px;
      font-size: 18px;
      background: #000;
      color: #0f0;
      border: 2px solid #0f0;
      border-radius: 10px;
      cursor: pointer;
      transition: 0.2s;
    }
    button:hover {
      background-color: #0f0;
      color: #000;
    }
    #mkInterface {
      display: none;
      margin-top: 40px;
    }
    textarea, #mkInterface input[type="password"] {
      background-color: #111;
      color: #00FF00;
      width: 90%;
      max-width: 400px;
      padding: 10px;
      font-size: 16px;
      border-radius: 12px;
      border: 2px solid #00FF00;
      resize: vertical;
      margin: 10px 0;
    }
    .action-buttons {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
    }
    .action-buttons button {
      margin: 5px;
    }
  </style>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
</head>
<body>

  <h1>حاسبة M.K</h1>
  <div class="calculator">
    <input type="text" id="display" readonly>
    <div class="buttons">
      <button onclick="press('7')">7</button>
      <button onclick="press('8')">8</button>
      <button onclick="press('9')">9</button>
      <button onclick="press('+')">+</button>
      <button onclick="press('4')">4</button>
      <button onclick="press('5')">5</button>
      <button onclick="press('6')">6</button>
      <button onclick="press('-')">-</button>
      <button onclick="press('1')">1</button>
      <button onclick="press('2')">2</button>
      <button onclick="press('3')">3</button>
      <button onclick="press('*')">*</button>
      <button onclick="press('0')">0</button>
      <button onclick="clearDisplay()">C</button>
      <button onclick="backspace()">←</button>
      <button onclick="press('/')">/</button>
      <button onclick="calculate()">=</button>
    </div>
  </div>

  <!-- واجهة M.K المخفية -->
  <div id="mkInterface">
    <h2>تشفير النص | AES</h2>
    <input id="sitePass" type="password" placeholder="ادخل كلمة السر" onkeyup="checkAccess(event)">
    <div id="mainArea" style="display:none">
      <input id="password" type="password" placeholder="كلمة المرور السرّية" />
      <textarea id="input" placeholder="اكتب النص هنا..."></textarea>
      <div class="action-buttons">
        <button onclick="encrypt();">تشفير</button>
        <button onclick="decrypt();">فك التشفير</button>
        <button onclick="copyResult();">نسخ</button>
        <button onclick="pasteClipboard();">لصق</button>
        <button onclick="clearAll();">مسح</button>
      </div>
      <textarea id="output" readonly></textarea>
    </div>
    <div id="statusMessage"></div>
  </div>

  <script>
    let display = document.getElementById("display");

    function press(val) {
      display.value += val;
    }

    function clearDisplay() {
      display.value = "";
    }

    function backspace() {
      display.value = display.value.slice(0, -1);
    }

    function calculate() {
      try {
        if (display.value === "99+00") {
          showMK();
          clearDisplay();
        } else {
          display.value = eval(display.value);
        }
      } catch {
        display.value = "خطأ";
      }
    }

    function showMK() {
      document.getElementById("mkInterface").style.display = "block";
      document.querySelector(".calculator").style.display = "none";
      playSound(); // تشغّل الصوت عند الدخول
    }

    // --- M.K FUNCTIONS ---
    const encodedPass = "S2guMDk="; // Base64 لـ Kh.09

    function checkAccess(e) {
      if (e.key === "Enter") {
        const input = document.getElementById("sitePass").value;
        if (btoa(input) === encodedPass) {
          document.getElementById("sitePass").style.display = 'none';
          document.getElementById("mainArea").style.display = 'block';
          showMessage("تم الدخول بنجاح!");
        } else {
          showMessage("كلمة المرور خاطئة!");
        }
      }
    }

    function encrypt() {
      const text = document.getElementById("input").value;
      const password = document.getElementById("password").value;
      if (!text || !password) return showMessage("يرجى إدخال النص وكلمة المرور");
      const encrypted = CryptoJS.AES.encrypt(text, password).toString();
      document.getElementById("output").value = encrypted;
      showMessage("تم التشفير!");
    }

    function decrypt() {
      const encrypted = document.getElementById("input").value;
      const password = document.getElementById("password").value;
      try {
        const bytes = CryptoJS.AES.decrypt(encrypted, password);
        const text = bytes.toString(CryptoJS.enc.Utf8);
        if (!text) throw new Error();
        document.getElementById("output").value = text;
        showMessage("تم فك التشفير!");
      } catch {
        showMessage("فك التشفير فشل!");
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
        showMessage("تم اللصق!");
      } catch {
        showMessage("لم يتم الوصول للحافظة!");
      }
    }

    function clearAll() {
      document.getElementById("input").value = '';
      document.getElementById("output").value = '';
      document.getElementById("password").value = '';
      showMessage("تم المسح.");
    }

    function showMessage(msg) {
      const status = document.getElementById("statusMessage");
      status.textContent = msg;
      setTimeout(() => status.textContent = '', 4000);
    }

    // إضافة صوت عند الدخول
    function playSound() {
      let audio = new Audio('https://www.soundjay.com/button/beep-07.wav'); // تأكد من وجود الرابط
      audio.play();
    }

    // تشويش الكود (Obfuscation) البسيط
    function btoa(str) {
      return window.btoa(str); // ترميز Base64 بديل، لتشويش كلمة السر
    }

  </script>

</body>
</html>
