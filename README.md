# Student-hub-
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Student Hub</title>
<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #0f172a;
  color: white;
  text-align: center;
}
input {
  padding: 10px;
  margin: 5px;
  border-radius: 6px;
  border: none;
}
button {
  padding: 10px 20px;
  margin: 5px;
  border: none;
  border-radius: 6px;
  background: #3b82f6;
  color: white;
  cursor: pointer;
}
button:hover { background: #2563eb; }
canvas {
  margin-top: 20px;
  border-radius: 10px;
}
#adminPanel { display: none; margin-top: 20px; }
</style>
</head>
<body>

<!-- LOGIN -->
<div id="loginScreen">
  <h2>Student Login</h2>
  <input type="text" id="username" placeholder="Enter your name"><br>
  <input type="password" id="password" placeholder="Enter a password"><br>
  <button onclick="signup()">Sign Up</button>
  <button onclick="login()">Login</button>
</div>

<!-- MAIN APP -->
<div id="app" style="display:none;">
  <h1 id="welcome"></h1>
  <canvas id="canvas" width="900" height="500"></canvas>

  <!-- ADMIN PANEL -->
  <div id="adminPanel">
    <h2>👑 Admin Panel</h2>
    <input type="text" id="banName" placeholder="Name to ban">
    <button onclick="banUser()">Ban User</button>
  </div>
</div>

<script>
// ADMIN PASSWORD
const ADMIN_PASSWORD = "admin123"; // change this to whatever you want

// Load users from localStorage
let users = JSON.parse(localStorage.getItem("users")) || [];
let bannedUsers = JSON.parse(localStorage.getItem("bannedUsers")) || [];

// SIGNUP
function signup(){
  const name = document.getElementById("username").value.trim();
  const pass = document.getElementById("password").value.trim();
  if(!name || !pass) return alert("Enter both name and password");

  if(users.some(u=>u.name===name)) return alert("Name already exists. Login instead.");

  users.push({name:name, password:pass, role:"student"});
  localStorage.setItem("users", JSON.stringify(users));
  alert("Account created! You can now login.");
}

// LOGIN
function login(){
  const name = document.getElementById("username").value.trim();
  const pass = document.getElementById("password").value.trim();
  if(!name || !pass) return alert("Enter both name and password");

  if(bannedUsers.includes(name)) return alert("You are banned from this hub.");

  const user = users.find(u=>u.name===name && u.password===pass);
  if(!user){
    if(name==="admin" && pass===ADMIN_PASSWORD){
      localStorage.setItem("currentUser","admin");
      showApp("Admin");
    } else {
      return alert("Invalid login");
    }
  } else {
    localStorage.setItem("currentUser",name);
    showApp(user.name);
  }
}

// SHOW APP
function showApp(userName){
  document.getElementById("loginScreen").style.display="none";
  document.getElementById("app").style.display="block";
  document.getElementById("welcome").innerText = "Welcome, " + userName;

  if(userName==="Admin") document.getElementById("adminPanel").style.display="block";
  drawCanvas();
}

// AUTO LOGIN
window.onload = function(){
  const saved = localStorage.getItem("currentUser");
  if(saved) showApp(saved);
}

// BAN USER (admin only)
function banUser(){
  const name = document.getElementById("banName").value.trim();
  if(!name) return;
  bannedUsers.push(name);
  localStorage.setItem("bannedUsers", JSON.stringify(bannedUsers));
  alert(name + " has been banned on this device.");
}

// CANVAS DASHBOARD
function drawCanvas(){
  const canvas = document.getElementById("canvas");
  const ctx = canvas.getContext("2d");
  const buttons = [
    {text:"Classroom", x:100, y:100, w:200, h:60, link:"https://classroom.google.com"},
    {text:"Canvas", x:350, y:100, w:200, h:60, link:"https://canvas.instructure.com"},
    {text:"ProgressBook", x:600, y:100, w:200, h:60, link:"https://www.progressbook.com"},
    {text:"SmartPass", x:250, y:250, w:200, h:60, link:"https://smartpass.app"}
  ];

  // Background
  ctx.fillStyle="#111";
  ctx.fillRect(0,0,canvas.width,canvas.height);

  // Buttons
  ctx.font="20px Arial";
  ctx.textBaseline="middle";
  ctx.textAlign="left";

  buttons.forEach(btn=>{
    ctx.fillStyle="#3b82f6";
    ctx.fillRect(btn.x, btn.y, btn.w, btn.h);
    ctx.fillStyle="white";
    ctx.fillText(btn.text, btn.x + 20, btn.y + btn.h/2);
  });

  // Click listener
  canvas.addEventListener("click", e=>{
    const rect = canvas.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;
    buttons.forEach(btn=>{
      if(x>btn.x && x<btn.x+btn.w && y>btn.y && y<btn.y+btn.h){
        window.open(btn.link,"_blank");
      }
    });
  });
}
</script>

</body>
</html>