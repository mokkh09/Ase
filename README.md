<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>دردشة شبيهة بالواتساب</title>
  <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/crypto-js@4.1.1/crypto-js.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f5f5f5;
      display: flex;
      flex-direction: column;
      justify-content: flex-end;
      align-items: center;
      min-height: 100vh;
      margin: 0;
    }

    #chatContainer {
      width: 100%;
      max-width: 600px;
      background-color: #fff;
      box-shadow: 0px 0px 15px rgba(0, 0, 0, 0.1);
      border-radius: 10px;
      margin-top: 50px;
      overflow: hidden;
      display: flex;
      flex-direction: column;
    }

    #messages {
      padding: 20px;
      overflow-y: auto;
      height: 400px;
    }

    .message {
      margin: 10px 0;
      padding: 10px;
      border-radius: 10px;
      max-width: 75%;
      word-wrap: break-word;
    }

    .message.sent {
      background-color: #e1ffc7;
      align-self: flex-end;
    }

    .message.received {
      background-color: #f1f1f1;
      align-self: flex-start;
    }

    .inputContainer {
      display: flex;
      justify-content: space-between;
      padding: 10px;
      background-color: #eee;
    }

    #inputMessage {
      width: 85%;
      padding: 10px;
      border: none;
      border-radius: 20px;
      background-color: #fff;
    }

    #sendButton {
      width: 10%;
      padding: 10px;
      background-color: #128C7E;
      color: white;
      border: none;
      border-radius: 50%;
      cursor: pointer;
    }

    footer {
      background-color: #128C7E;
      color: white;
      width: 100%;
      text-align: center;
      padding: 5px;
      position: fixed;
      bottom: 0;
    }
  </style>
</head>
<body>
  <div id="chatContainer">
    <div id="messages"></div>
    <div class="inputContainer">
      <input type="text" id="inputMessage" placeholder="اكتب رسالتك هنا..." />
      <button id="sendButton" onclick="sendMessage()">إرسال</button>
    </div>
  </div>

  <footer>
    <p>مطور بواسطة M.Khasroof</p>
  </footer>

  <script>
    // Firebase configuration
    const firebaseConfig = {
      apiKey: "YOUR_FIREBASE_API_KEY",
      authDomain: "YOUR_FIREBASE_AUTH_DOMAIN",
      databaseURL: "YOUR_FIREBASE_DATABASE_URL",
      projectId: "YOUR_PROJECT_ID",
      storageBucket: "YOUR_STORAGE_BUCKET",
      messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
      appId: "YOUR_APP_ID"
    };
    const app = firebase.initializeApp(firebaseConfig);
    const database = firebase.database(app);

    const roomId = "chat_room_1"; // Room ID for the chat

    function sendMessage() {
      const inputMessage = document.getElementById("inputMessage");
      const messageText = inputMessage.value.trim();

      if (messageText !== "") {
        // Save the message to Firebase
        const message = {
          text: messageText,
          timestamp: new Date().toISOString(),
          sender: "User", // You can add dynamic user name
        };

        // Save message to Firebase Database
        database.ref("messages/" + roomId).push(message);

        inputMessage.value = ""; // Clear the input field
      }
    }

    // Fetch messages from Firebase and display them
    database.ref("messages/" + roomId).on("child_added", function(snapshot) {
      const message = snapshot.val();
      const messageElement = document.createElement("div");
      messageElement.classList.add("message");
      messageElement.classList.add(message.sender === "User" ? "sent" : "received");

      messageElement.textContent = message.text;

      document.getElementById("messages").appendChild(messageElement);
      document.getElementById("messages").scrollTop = document.getElementById("messages").scrollHeight; // Scroll to bottom
    });
  </script>
</body>
</html>
