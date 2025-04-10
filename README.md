<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>الدردشة | Firebase</title>
  <script src="https://www.gstatic.com/firebasejs/8.3.2/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.3.2/firebase-database.js"></script>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background-color: #1A1A1D;
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
    }

    button:hover {
      background-color: #00FF00;
      color: #000;
    }

    .messages {
      width: 90%;
      max-width: 600px;
      background-color: #111;
      padding: 15px;
      margin-top: 20px;
      max-height: 300px;
      overflow-y: scroll;
      color: #F1F1F1;
      border-radius: 12px;
    }

    footer {
      position: fixed;
      bottom: 20px;
      width: 100%;
      text-align: center;
      color: #00FF00;
    }
  </style>
</head>
<body>

  <h2>غرفة الدردشة | Firebase</h2>

  <input id="username" type="text" placeholder="أدخل اسمك" />
  <input id="roomID" type="text" placeholder="أدخل معرف الغرفة (على سبيل المثال: room1)" />
  <textarea id="messageInput" placeholder="اكتب رسالتك هنا..."></textarea>

  <button onclick="sendMessage()">إرسال الرسالة</button>

  <div class="messages" id="messages"></div>

  <footer>
    <p>مطور: M.Khasroof</p>
  </footer>

  <script>
    // إعدادات Firebase
    const firebaseConfig = {
      apiKey: "YOUR_API_KEY",
      authDomain: "YOUR_AUTH_DOMAIN",
      databaseURL: "YOUR_DATABASE_URL",
      projectId: "YOUR_PROJECT_ID",
      storageBucket: "YOUR_STORAGE_BUCKET",
      messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
      appId: "YOUR_APP_ID"
    };

    // تهيئة Firebase
    firebase.initializeApp(firebaseConfig);
    const database = firebase.database();

    // إرسال الرسالة
    function sendMessage() {
      const username = document.getElementById("username").value;
      const roomID = document.getElementById("roomID").value;
      const message = document.getElementById("messageInput").value;
      
      if (username && roomID && message) {
        // حفظ الرسالة في قاعدة البيانات ضمن الغرفة المحددة
        const messagesRef = database.ref('rooms/' + roomID + '/messages');
        messagesRef.push({
          username: username,
          message: message,
          timestamp: new Date().toISOString()
        }).then(() => {
          // مسح النص بعد الإرسال
          document.getElementById("messageInput").value = '';
        }).catch((error) => {
          console.error("Error writing message to Firebase: ", error);
        });
      } else {
        alert('يرجى إدخال اسمك، معرف الغرفة والرسالة!');
      }
    }

    // استرجاع الرسائل وعرضها في غرفة الدردشة
    function loadMessages() {
      const roomID = document.getElementById("roomID").value;
      
      if (roomID) {
        const messagesRef = database.ref('rooms/' + roomID + '/messages');
        messagesRef.on('child_added', function(snapshot) {
          const messageData = snapshot.val();
          const messageElement = document.createElement('div');
          messageElement.textContent = `${messageData.username}: ${messageData.message}`;
          document.getElementById('messages').appendChild(messageElement);
        });
      }
    }

    // عند تغيير معرف الغرفة، نقوم بتحميل الرسائل من جديد
    document.getElementById("roomID").addEventListener('input', loadMessages);
  </script>

</body>
</html>
