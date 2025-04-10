<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>دردشة عامة</title>
  <style>
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

    #messageInput {
      background-color: #111;
      color: #00FF00;
      width: 80%;
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

    #chat {
      background-color: #111;
      color: #F1F1F1;
      width: 90%;
      max-width: 600px;
      height: 300px;
      overflow-y: scroll;
      padding: 15px;
      border-radius: 12px;
      border: 2px solid #00FF00;
      margin-bottom: 20px;
      box-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
    }

    #users {
      background-color: #111;
      color: #00FF00;
      width: 90%;
      max-width: 600px;
      height: 150px;
      overflow-y: scroll;
      padding: 15px;
      border-radius: 12px;
      border: 2px solid #00FF00;
      margin-bottom: 20px;
      box-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
    }

    footer {
      position: fixed;
      bottom: 20px;
      width: 100%;
      text-align: center;
    }

    .signature {
      font-family: 'Share Tech Mono', monospace;
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

  <h2>دردشة عامة</h2>

  <div id="chat"></div>

  <div id="users">
    <h3>المستخدمين الحاليين:</h3>
    <ul id="userList"></ul>
  </div>

  <input id="messageInput" type="text" placeholder="اكتب رسالتك هنا..." />
  <button onclick="sendMessage()">إرسال الرسالة</button>

  <footer>
    <p class="signature" id="sig">M.Khasroof</p>
  </footer>

  <!-- Firebase SDK -->
  <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>

  <script>
    // Firebase Configuration
    const firebaseConfig = {
      apiKey: "YOUR_API_KEY",
      authDomain: "YOUR_AUTH_DOMAIN",
      databaseURL: "YOUR_DATABASE_URL",
      projectId: "YOUR_PROJECT_ID",
      storageBucket: "YOUR_STORAGE_BUCKET",
      messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
      appId: "YOUR_APP_ID"
    };

    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    const database = firebase.database();

    // Register user
    function registerUser() {
      let user = localStorage.getItem('chatUserName');
      if (!user) {
        user = "مستخدم " + new Date().getTime(); // Generate a unique username
        localStorage.setItem('chatUserName', user);
      }
      return user;
    }

    // Function to load messages from Firebase
    function loadMessages() {
      const chatDiv = document.getElementById('chat');
      chatDiv.innerHTML = '';
      database.ref('messages').on('child_added', function(snapshot) {
        const message = snapshot.val();
        const msgElement = document.createElement('div');
        msgElement.textContent = `${message.user}: ${message.text}`;
        chatDiv.appendChild(msgElement);
      });
    }

    // Function to load users from Firebase
    function loadUsers() {
      const userList = document.getElementById('userList');
      userList.innerHTML = '';
      database.ref('users').on('child_added', function(snapshot) {
        const user = snapshot.val();
        const userElement = document.createElement('li');
        userElement.textContent = user;
        userList.appendChild(userElement);
      });
    }

    // Send message to Firebase
    function sendMessage() {
      const messageInput = document.getElementById('messageInput');
      const message = messageInput.value.trim();
      const user = registerUser();
      if (message === '') return;

      database.ref('messages').push({
        text: message,
        user: user
      });

      // Add user to the list if they aren't already there
      const usersRef = database.ref('users');
      usersRef.once('value', function(snapshot) {
        const users = snapshot.val() || {};
        if (!Object.values(users).includes(user)) {
          usersRef.push(user);
        }
      });

      messageInput.value = '';
    }

    // Load messages and users when the page is loaded
    window.onload = function() {
      loadMessages();
      loadUsers();
    }
  </script>

</body>
</html>
