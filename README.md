<!mousaab.html>
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



<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>أدمن العميل</title>
<style>
body {background:#111; color:#fff; font-family:Arial; text-align:center;}
input, button {margin:10px; padding:10px; border-radius:5px;}
button {cursor:pointer; background:#00ffcc; border:none;}
</style>
</head>
<body>

<h2>🔐 تسجيل دخول العميل</h2>
<div id="loginDiv">
<input type="text" id="clientName" placeholder="اسم العميل">
<input type="password" id="clientPass" placeholder="كلمة المرور">
<button onclick="login()">دخول</button>
</div>

<div id="clientPanel" style="display:none;">
<h2>🛠 لوحة التحكم الخاصة بك</h2>
<h3>➕ إضافة / تعديل المنتجات</h3>
<input id="prodName" placeholder="اسم المنتج">
<input id="prodImg" placeholder="رابط الصورة">
<input id="prodWA" placeholder="رابط واتساب">
<input id="prodIG" placeholder="رابط إنستغرام">
<input id="prodFB" placeholder="رابط فيسبوك">
<button onclick="addProduct()">حفظ المنتج</button>

<h3>📊 عداد زيارات الرابط العام</h3>
<p id="visitCount">0</p>
</div>

<script>
let clients = JSON.parse(localStorage.getItem("clients")) || [];
let expiryDates = JSON.parse(localStorage.getItem("expiryDates")) || {};
let products = JSON.parse(localStorage.getItem("products")) || {};
let currentClient = null;

function login(){
  let name=document.getElementById("clientName").value;
  let pass=document.getElementById("clientPass").value;
  let c = clients.find(c=>c.name===name && c.password===pass);
  if(c){
    currentClient=name;
    document.getElementById("loginDiv").style.display="none";
    document.getElementById("clientPanel").style.display="block";
    if(!products[currentClient]) products[currentClient]=[];
    localStorage.setItem("products", JSON.stringify(products));
    document.getElementById("visitCount").innerText = c.visits;
  } else alert("❌ بيانات الدخول خاطئة");
}

function addProduct(){
  let pName=document.getElementById("prodName").value;
  let pImg=document.getElementById("prodImg").value;
  let pWA=document.getElementById("prodWA").value;
  let pIG=document.getElementById("prodIG").value;
  let pFB=document.getElementById("prodFB").value;
  if(!pName || !pImg){ alert("املأ الاسم والصورة"); return;}
  products[currentClient].push({name:pName,img:pImg,whatsapp:pWA,instagram:pIG,facebook:pFB});
  localStorage.setItem("products", JSON.stringify(products));
  alert("✅ تم حفظ المنتج");
  document.getElementById("prodName").value="";
  document.getElementById("prodImg").value="";
  document.getElementById("prodWA").value="";
  document.getElementById("prodIG").value="";
  document.getElementById("prodFB").value="";
}
</script>

</body>
</html>


<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>صفحة النشر العامة</title>
<style>
body {font-family:Arial; background:#111; color:#fff; text-align:center;}
.product {background:#1c1c1c; margin:15px; padding:15px; border-radius:10px;}
img {width:200px; border-radius:10px;}
button {margin:5px; padding:10px 15px; border:none; border-radius:6px; cursor:pointer;}
</style>
</head>
<body>

<h1>🛍 منتجات العملاء</h1>
<div id="allProducts"></div>

<script>
let products = JSON.parse(localStorage.getItem("products")) || {};
let expiryDates = JSON.parse(localStorage.getItem("expiryDates")) || {};
let clients = JSON.parse(localStorage.getItem("clients")) || [];

let container=document.getElementById("allProducts");
let now = new Date();

for(let clientName in products){
  let expiry = new Date(expiryDates[clientName]);
  if(now <= expiry){
    products[clientName].forEach(p=>{
      container.innerHTML += `
      <div class="product">
        <h3>${p.name} - ${clientName}</h3>
        <img src="${p.img}">
        <br>
        <button onclick="window.open('${p.whatsapp}')">📲 واتساب</button>
        <button onclick="window.open('${p.instagram}')">📸 إنستغرام</button>
        <button onclick="window.open('${p.facebook}')">📘 فيسبوك</button>
      </div>`;
    });
  }
}

if(container.innerHTML === "") container.innerHTML="<h2>🔒 انتهت صلاحية جميع الاشتراكات، الرجاء التجديد</h2>";
</script>

</body>
</html>
