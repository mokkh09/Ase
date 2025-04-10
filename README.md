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
      width: 200px;
      height: 300px;
      overflow-y: scroll;
      padding: 15px;
      border-radius: 12px;
      border: 2px solid #00FF00;
      box-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
      margin-bottom: 20px;
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
    <h3>المستخدمين:</h3>
    <ul id="userList"></ul>
  </div>

  <input id="messageInput" type="text" placeholder="اكتب رسالتك هنا..." />
  <button onclick="sendMessage()">إرسال الرسالة</button>

  <footer>
    <p class="signature" id="sig">M.Khasroof</p>
  </footer>

  <script>
    // Function to load messages from localStorage
    function loadMessages() {
      const messages = JSON.parse(localStorage.getItem('chatMessages')) || [];
      const chatDiv = document.getElementById('chat');
      chatDiv.innerHTML = ''; // Clear previous messages
      messages.forEach(message => {
        const msgElement = document.createElement('div');
        msgElement.textContent = message.text;
        chatDiv.appendChild(msgElement);
      });
    }

    // Function to load users from localStorage
    function loadUsers() {
      const users = JSON.parse(localStorage.getItem('chatUsers')) || [];
      const userList = document.getElementById('userList');
      userList.innerHTML = ''; // Clear previous users
      users.forEach(user => {
        const userElement = document.createElement('li');
        userElement.textContent = user;
        userList.appendChild(userElement);
      });
    }

    // Function to get or set the user's name
    function getUserName() {
      let user = localStorage.getItem('chatUserName');
      if (!user) {
        user = prompt("أدخل اسمك:");
        if (user) {
          localStorage.setItem('chatUserName', user); // Store the name for future sessions
        }
      }
      return user;
    }

    // Function to send a message
    function sendMessage() {
      const messageInput = document.getElementById('messageInput');
      const message = messageInput.value.trim();
      const user = getUserName(); // Get the user's name once and reuse it
      if (message === '' || !user) return; // Ignore empty messages or missing user
      const messages = JSON.parse(localStorage.getItem('chatMessages')) || [];
      messages.push({ text: message, user: user });
      localStorage.setItem('chatMessages', JSON.stringify(messages)); // Save messages to localStorage

      const users = JSON.parse(localStorage.getItem('chatUsers')) || [];
      if (!users.includes(user)) {
        users.push(user);
        localStorage.setItem('chatUsers', JSON.stringify(users)); // Save users to localStorage
      }

      loadMessages(); // Reload messages
      loadUsers(); // Reload users
      messageInput.value = ''; // Clear input
      messageInput.focus(); // Focus back on input
    }

    // Load messages and users when the page is loaded
    window.onload = function() {
      loadMessages();
      loadUsers();
    }

  </script>

</body>
</html>
