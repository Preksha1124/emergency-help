<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Emergency App</title>
  <style>
    body { font-family: Arial; text-align: center; padding: 40px; background: #f0f8ff; }
    input, button { margin: 10px; padding: 10px; font-size: 16px; width: 250px; }
    button { background: #e53935; color: white; border: none; border-radius: 6px; }
    #sosOnly { display: none; }
    #sosButton { font-size: 24px; padding: 20px; width: 300px; background: red; color: white; border-radius: 10px; }
  </style>
</head>
<body>
  <h1>🚨 Emergency App</h1>

  <div id="auth">
    <input type="text" id="name" placeholder="Your Name" /><br>
    <input type="tel" id="phone" placeholder="Phone (10 digits)" /><br>
    <input type="password" id="password" placeholder="Password" /><br>
    <input type="tel" id="contact1" placeholder="Emergency Contact (10 digits)" /><br>
    <button onclick="signup()">Sign Up</button>
    <button onclick="login()">Login</button>
  </div>

  <div id="sosOnly">
    <button id="sosButton" onclick="sendPanicAlert()">🚨 SOS</button>
    <div id="output"></div>
    <br><br>
    <button onclick="logout()">Logout</button>
  </div>

  <script>
    function signup() {
      const name = document.getElementById("name").value.trim();
      const phone = '+91' + document.getElementById("phone").value.trim();
      const password = document.getElementById("password").value;
      const contact1 = document.getElementById("contact1").value.trim();

      if (!name || phone.length !== 13 || !password || contact1.length !== 10) {
        alert("Please fill all fields correctly.");
        return;
      }

      const user = { name, phone, password, emergencyContact: contact1 };
      localStorage.setItem(phone, JSON.stringify(user));
      localStorage.setItem("loggedInUser", phone);
      showSOSOnly(user);
    }

    function login() {
      const phone = '+91' + document.getElementById("phone").value.trim();
      const password = document.getElementById("password").value;
      const user = JSON.parse(localStorage.getItem(phone));

      if (user && user.password === password) {
        localStorage.setItem("loggedInUser", phone);
        showSOSOnly(user);
      } else {
        alert("Invalid credentials.");
      }
    }

    function showSOSOnly(user) {
      document.getElementById("auth").style.display = "none";
      document.getElementById("sosOnly").style.display = "block";
    }

    function sendPanicAlert() {
      const phone = localStorage.getItem("loggedInUser");
      const user = JSON.parse(localStorage.getItem(phone));
      const contact = user.emergencyContact;

      if (!contact || contact.length !== 10) {
        alert("Emergency contact not found.");
        return;
      }

      const output = document.getElementById("output");
      navigator.geolocation.getCurrentPosition(pos => {
        const lat = pos.coords.latitude;
        const lon = pos.coords.longitude;
        const link = `https://maps.google.com/?q=${lat},${lon}`;
        const message = `🚨 Emergency! I need help. My live location: ${link}`;
        output.innerHTML = `<a href="${link}" target="_blank">${link}</a>`;

        const waLink = `https://wa.me/91${contact}?text=${encodeURIComponent(message)}`;
        window.open(waLink, "_blank");
      }, err => {
        output.innerText = "Location access denied.";
      });
    }

    function logout() {
      localStorage.removeItem("loggedInUser");
      document.getElementById("auth").style.display = "block";
      document.getElementById("sosOnly").style.display = "none";
    }

    window.onload = () => {
      const phone = localStorage.getItem("loggedInUser");
      if (phone) {
        const user = JSON.parse(localStorage.getItem(phone));
        if (user) {
          showSOSOnly(user);
        }
      }
    };
  </script>
</body>
</html>
 
