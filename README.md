<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>أدمن رئيسي</title>
<style>
body {background:#111; color:#fff; font-family:Arial; text-align:center;}
input, button {margin:10px; padding:10px; border-radius:5px;}
button {cursor:pointer; background:#00ffcc; border:none;}
</style>
</head>
<body>

<h2>🔐 تسجيل دخول الأدمن الرئيسي</h2>

<div id="loginDiv">
<input type="password" id="masterPass" placeholder="كلمة المرور">
<button onclick="login()">دخول</button>
</div>

<div id="adminPanel" style="display:none;">
<h2>👑 لوحة التحكم الرئيسية</h2>

<h3>➕ إضافة عميل جديد</h3>
<input id="clientName" placeholder="اسم العميل">
<input id="clientPass" placeholder="كلمة مرور العميل">
<button onclick="addClient()">إضافة العميل</button>

<h3>📊 عداد الزيارات لكل عميل</h3>
<div id="clientsList"></div>
</div>

<script>
const MASTER_PASS = "mousaab0123";
let clients = JSON.parse(localStorage.getItem("clients")) || [];
let expiryDates = JSON.parse(localStorage.getItem("expiryDates")) || {};

function login(){
  let pass = document.getElementById("masterPass").value;
  if(pass === MASTER_PASS){
    document.getElementById("loginDiv").style.display="none";
    document.getElementById("adminPanel").style.display="block";
    showClients();
  } else alert("❌ كلمة المرور خاطئة");
}

function addClient(){
  let name = document.getElementById("clientName").value;
  let pass = document.getElementById("clientPass").value;
  if(name && pass){
    clients.push({name:name, password:pass, visits:0});
    expiryDates[name] = new Date(new Date().setMonth(new Date().getMonth() + 1)); // شهر جديد
    localStorage.setItem("clients", JSON.stringify(clients));
    localStorage.setItem("expiryDates", JSON.stringify(expiryDates));
    document.getElementById("clientName").value="";
    document.getElementById("clientPass").value="";
    showClients();
    alert("✅ تم إضافة العميل");
  } else alert("املأ الاسم وكلمة المرور");
}

function showClients(){
  let div = document.getElementById("clientsList");
  div.innerHTML="";
  clients.forEach((c,i)=>{
    let expiry = new Date(expiryDates[c.name]);
    div.innerHTML+=`
    <div style="margin:10px; padding:10px; background:#1c1c1c; border-radius:8px;">
      <b>${c.name}</b> | كلمة المرور: ${c.password} | زيارات: ${c.visits} | انتهاء الاشتراك: ${expiry.toDateString()}
      <br>
      <button onclick="deleteClient(${i})">حذف العميل</button>
      <button onclick="extendClient('${c.name}')">تمديد الاشتراك شهر</button>
    </div>`;
  });
}

function deleteClient(i){
  if(confirm("هل تريد حذف هذا العميل؟")){
    let name = clients[i].name;
    clients.splice(i,1);
    delete expiryDates[name];
    localStorage.setItem("clients", JSON.stringify(clients));
    localStorage.setItem("expiryDates", JSON.stringify(expiryDates));
    showClients();
  }
}

function extendClient(name){
  let newExpiry = new Date(expiryDates[name]);
  newExpiry.setMonth(newExpiry.getMonth() + 1);
  expiryDates[name] = newExpiry;
  localStorage.setItem("expiryDates", JSON.stringify(expiryDates));
  alert("✅ تم تمديد الاشتراك لشهر إضافي");
  showClients();
}
</script>

</body>
</html>