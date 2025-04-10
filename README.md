<head>
  <meta charset="UTF-8">
  <title>تشفير النص | AES</title>
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
      overflow: hidden;
    }

    h2 {
      font-size: 34px;
      color: #00FF00;
      text-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
      margin-bottom: 20px;
      animation: glitch 1s infinite;
    }

    @keyframes glitch {
      0% { transform: translate(2px, 2px) skew(0deg); }
      20% { transform: translate(-2px, -2px) skew(-1deg); }
      40% { transform: translate(1px, 1px) skew(0deg); }
      60% { transform: translate(-1px, -1px) skew(1deg); }
      80% { transform: translate(2px, 2px) skew(0deg); }
      100% { transform: translate(0px, 0px) skew(0deg); }
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
      font-family: 'Share Tech Mono', monospace;
      animation: glitch 1s infinite;
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

    /* Matrix rain effect */
    .rain {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      pointer-events: none;
      background-color: rgba(0, 0, 0, 0.1);
      z-index: -1;
    }
    .rain span {
      position: absolute;
      color: #00FF00;
      font-size: 20px;
      animation: rain 0.5s linear infinite;
    }

    @keyframes rain {
      0% { transform: translateY(-100vh); }
      100% { transform: translateY(100vh); }
    }
  </style>
</head>

<body>
  <div class="rain" id="rain"></div>
  <h2>تشفير النص | AES</h2>
  <input id="sitePass" type="password" placeholder="ادخل كلمة سر الدخول" onkeyup="checkAccess(event)">
  <div id="mainArea" style="display:none">
    <input id="password" type="password" placeholder="كلمة المرور السرّية" />
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
      localStorage.setItem("lastResult", ciphertext);  // Save the last result
      showMessage("تم التشفير بنجاح!");
      playSound();  // Play sound on action
    }

    function decrypt() {
      const code = document.getElementById("input").value;
      const password = document.getElementById("password").value;
      try {
        const bytes = CryptoJS.AES.decrypt(code, password);
        const originalText = bytes.toString(CryptoJS.enc.Utf8);
        if (!originalText) throw new Error();
        document.getElementById("output").value = originalText;
        localStorage.setItem("lastResult", originalText);  // Save the last result
        showMessage("تم فك التشفير بنجاح!");
      } catch {
        showMessage("فشل فك التشفير! تأكد من النص وكلمة المرور.");
      }
      playSound();  // Play sound on action
    }

    function copyResult() {
      const output = document.getElementById("output");
      output.select();
      document.execCommand("copy");
      showMessage("تم النسخ!");
      playSound();  // Play sound on action
    }

    async function pasteClipboard() {
      try {
        const text = await navigator.clipboard.readText();
        document.getElementById("input").value = text;
        showMessage("تم اللصق من الحافظة!");
        playSound();  // Play sound on action
      } catch {
        showMessage("لم يتمكن من الوصول للحافظة!");
      }
    }

    function shareWhatsApp() {
      const text = document.getElementById("output").value;
      if (!text) return alert("لا يوجد نص للمشاركة!");
      const url = "https://wa.me/?text=" + encodeURIComponent(text);
      window.open(url, "_blank");
      playSound();  // Play sound on action
    }

    function clearAll() {
      document.getElementById("input").value = '';
      document.getElementById("output").value = '';
      document.getElementById("password").value = '';
      showMessage("تم المسح.");
      playSound();  // Play sound on action
    }

    function showMessage(message) {
      const status = document.getElementById("statusMessage");
      status.textContent = message;
      setTimeout(() => status.textContent = '', 4000);
    }

    function playSound() {
      const sound = new Audio('https://www.soundjay.com/button/beep-07.wav');
      sound.play();
    }

    function createRainEffect() {
      const rainContainer = document.getElementById("rain");
      for (let i = 0; i < 100; i++) {
        const span = document.createElement("span");
        span.style.left = `${Math.random() * 100}%`;
        span.style.animationDuration = `${Math.random() * 2 + 2}s`;
        span.textContent = "1";
        rainContainer.appendChild(span);
      }
    }

    // Load last result from LocalStorage
    window.onload = function() {
      const lastResult = localStorage.getItem("lastResult");
      if (lastResult) {
        document.getElementById("output").value = lastResult;
      }
      createRainEffect();  // Create Matrix rain effect
    };
  </script>
</body>
