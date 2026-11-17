[index.html.html](https://github.com/user-attachments/files/34980459/index.html.html)
# CropShield
Smart Crop Monitoring System
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CropShield | Smart Crop Monitoring</title>
<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#f3f7f1;color:#203126}
header{background:#176b3a;color:white;padding:22px 6%;display:flex;justify-content:space-between;align-items:center;gap:12px}
header h1{margin:0;font-size:25px}
header p{margin:5px 0 0;font-size:13px;opacity:.9}
.tag{background:#e2f4e8;color:#176b3a;padding:8px 12px;border-radius:20px;font-size:12px;font-weight:bold}
main{max-width:1100px;margin:28px auto;padding:0 18px 35px}
.welcome{margin-bottom:22px}
.welcome h2{margin:0 0 7px;font-size:24px}
.welcome p{margin:0;color:#647267;line-height:1.5}
.grid{display:grid;grid-template-columns:repeat(3,1fr);gap:16px}
.card{background:white;border-radius:14px;padding:19px;box-shadow:0 3px 14px rgba(25,60,35,.07);min-width:0}
.card h3{margin:0 0 13px;font-size:15px;color:#526357}
.reading{font-size:32px;font-weight:bold;color:#176b3a}
.unit{font-size:15px;color:#66756a}
.hint{margin-top:9px;font-size:12px;color:#748078}
.status-card{margin-top:17px;display:flex;align-items:center;justify-content:space-between;gap:14px}
.status{display:inline-block;border-radius:20px;padding:8px 13px;font-size:13px;font-weight:bold;background:#fff0cf;color:#855600}
.alert{margin-top:17px;border-left:5px solid #e2a52b;background:#fff8e8;padding:15px 17px;border-radius:9px;line-height:1.5}
.alert strong{display:block;margin-bottom:4px}
.section-title{margin:30px 0 14px}
.section-title h2{margin:0 0 5px;font-size:20px}
.section-title p{margin:0;color:#68766c;font-size:13px}
.charts{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:16px}
.chart-card{background:white;border-radius:14px;padding:18px;box-shadow:0 3px 14px rgba(25,60,35,.07);min-width:0}
.chart-card.wide{grid-column:1/-1}
canvas{width:100%;height:220px;display:block}
.chart-note{font-size:11px;color:#748078;margin:8px 0 0}
button{border:0;background:#176b3a;color:white;border-radius:8px;padding:11px 16px;font-weight:bold;cursor:pointer}
button:hover{background:#10552d}
.note{margin-top:18px;font-size:12px;color:#68766c;line-height:1.5}
footer{text-align:center;color:#78847b;font-size:12px;padding:20px}
@media(max-width:700px){
header{align-items:flex-start;flex-direction:column}
.grid,.charts{grid-template-columns:1fr}
.card.wide{grid-column:auto}
.status-card{align-items:flex-start;flex-direction:column}
}
</style>
</head>

<body>
<header>
<div>
<h1>🌱 CropShield</h1>
<p>Smart Crop Stress Early-Warning System</p>
</div>
<span class="tag"> </span>
</header>

<main>
<section class="welcome">
<h2>Crop Monitoring Dashboard</h2>
<p>Monitor field conditions and identify possible crop stress early.</p>
</section>

<section class="grid" aria-label="Sensor readings">
<article class="card">
<h3>Soil Moisture</h3>
<div><span class="reading" id="moisture">28</span><span class="unit"> %</span></div>
<div class="hint">Demo sensor reading</div>
</article>

<article class="card">
<h3>Temperature</h3>
<div><span class="reading" id="temperature">31</span><span class="unit"> °C</span></div>
<div class="hint">Demo sensor reading</div>
</article>

<article class="card">
<h3>Humidity</h3>
<div><span class="reading" id="humidity">62</span><span class="unit"> %</span></div>
<div class="hint">Demo sensor reading</div>
</article>
</section>

<section class="card status-card">
<div>
<h3>Crop condition</h3>
<span class="status" id="cropStatus">Needs attention</span>
<p class="hint" id="statusMessage">Soil moisture is below the demo threshold.</p>
</div>
<button onclick="updateDemo()">Update demo readings</button>
</section>

<section class="alert" id="alertBox">
<strong>⚠ Early warning</strong>
Soil moisture is low in this example. Check the soil and water the crop if needed.
</section>

<div class="section-title">
<h2>Sensor trend graphs</h2>
<p>These graphs show how each measurement changes over the latest demo readings.</p>
</div>

<section class="charts">
<article class="chart-card">
<h3>Soil moisture (%)</h3>
<canvas id="moistureChart"></canvas>
<p class="chart-note">Lower moisture can indicate that the crop may need watering.</p>
</article>

<article class="chart-card">
<h3>Temperature (°C)</h3>
<canvas id="temperatureChart"></canvas>
<p class="chart-note">Track temperature changes over time.</p>
</article>

<article class="chart-card">
<h3>Humidity (%)</h3>
<canvas id="humidityChart"></canvas>
<p class="chart-note">Track changes in surrounding air humidity.</p>
</article>

<article class="chart-card wide">
<h3>Daily history – all readings</h3>
<canvas id="historyChart"></canvas>
<p class="chart-note">Combined view of the latest readings.</p>
</article>
</section>

<p class="note">
<strong>Important:</strong> All readings and graphs are sample demo data.
They are not live sensor measurements. In a later step, we will connect
the ESP32 and sensors so the dashboard can show actual field data.
</p>
</main>

<footer>CropShield student project · Smart agriculture monitoring</footer>

<script>
const history = [
{time:"9 AM",moisture:65,temperature:25,humidity:75},
{time:"10 AM",moisture:58,temperature:27,humidity:70},
{time:"11 AM",moisture:49,temperature:29,humidity:67},
{time:"12 PM",moisture:42,temperature:32,humidity:61},
{time:"1 PM",moisture:35,temperature:33,humidity:58},
{time:"2 PM",moisture:28,temperature:31,humidity:62}
];

function drawLineChart(canvasId,values,color,min,max,suffix){
const canvas=document.getElementById(canvasId);
const rect=canvas.getBoundingClientRect();
const dpr=window.devicePixelRatio||1;
const w=Math.max(rect.width,280),h=220;

canvas.width=w*dpr;
canvas.height=h*dpr;

const ctx=canvas.getContext("2d");
ctx.scale(dpr,dpr);

const left=42,right=12,top=15,bottom=32;
const plotW=w-left-right,plotH=h-top-bottom;

ctx.clearRect(0,0,w,h);
ctx.font="11px Arial";
ctx.fillStyle="#718075";
ctx.strokeStyle="#e7eee8";
ctx.lineWidth=1;

for(let i=0;i<=4;i++){
let y=top+plotH*i/4;
ctx.beginPath();
ctx.moveTo(left,y);
ctx.lineTo(w-right,y);
ctx.stroke();
ctx.fillText(String(Math.round(max-(max-min)*i/4))+suffix,4,y+4);
}

const pts=values.map((v,i)=>({
x:left+(values.length===1?0:i*plotW/(values.length-1)),
y:top+(max-v)/(max-min)*plotH
}));

ctx.strokeStyle=color;
ctx.lineWidth=2.5;
ctx.beginPath();
pts.forEach((p,i)=>i?ctx.lineTo(p.x,p.y):ctx.moveTo(p.x,p.y));
ctx.stroke();

pts.forEach(p=>{
ctx.fillStyle=color;
ctx.beginPath();
ctx.arc(p.x,p.y,3.5,0,Math.PI*2);
ctx.fill();
});

const labels=history.map(x=>x.time);
ctx.fillStyle="#718075";
ctx.textAlign="center";

labels.forEach((label,i)=>{
if(i%2===0||i===labels.length-1){
let x=left+(labels.length===1?0:i*plotW/(labels.length-1));
ctx.fillText(label,x,h-10);
}
});
}

function drawAllCharts(){
drawLineChart("moistureChart",history.map(x=>x.moisture),"#25834b",0,100,"%");
drawLineChart("temperatureChart",history.map(x=>x.temperature),"#d27b24",0,50,"°");
drawLineChart("humidityChart",history.map(x=>x.humidity),"#397eb8",0,100,"%");

const scaled=history.map(x=>((x.moisture+x.temperature*2+x.humidity)/3));
drawLineChart("historyChart",scaled,"#176b3a",0,100,"%");
}

function showReadings(r){
document.getElementById("moisture").textContent=r.moisture;
document.getElementById("temperature").textContent=r.temperature;
document.getElementById("humidity").textContent=r.humidity;

const status=document.getElementById("cropStatus");
const message=document.getElementById("statusMessage");
const alert=document.getElementById("alertBox");

if(r.moisture<35){
status.textContent="Needs attention";
message.textContent="Soil moisture is below the demo threshold.";
status.style.background="#fff0cf";
status.style.color="#855600";
alert.innerHTML="<strong>⚠ Early warning</strong>Soil moisture is low in this example. Check the soil and water the crop if needed.";
alert.style.borderColor="#e2a52b";
alert.style.background="#fff8e8";
}else{
status.textContent="Normal";
message.textContent="The example soil moisture is within the demo range.";
status.style.background="#e2f4e8";
status.style.color="#176b3a";
alert.innerHTML="<strong>✓ Conditions look normal</strong>The example soil moisture is above the demo warning threshold.";
alert.style.borderColor="#25834b";
alert.style.background="#eff9f1";
}
}

async function updateDemo(){
const r={
time:new Date().toLocaleTimeString(),
moisture:Math.floor(Math.random()*61)+20,
temperature:Math.floor(Math.random()*11)+25,
humidity:Math.floor(Math.random()*31)+45
};

history.push(r);
if(history.length>8)history.shift();

showReadings(r);
drawAllCharts();

// Send the readings to Firebase
if(window.saveToFirebase){
await window.saveToFirebase(r);
}else{
console.error("Firebase is not ready yet.");
}
}

showReadings(history[history.length-1]);
drawAllCharts();
window.addEventListener("resize",drawAllCharts);
</script>

<script type="module">
import {initializeApp} from "https://www.gstatic.com/firebasejs/12.0.0/firebase-app.js";
import {getDatabase,ref,set} from "https://www.gstatic.com/firebasejs/12.0.0/firebase-database.js";

const firebaseConfig = {
apiKey: "AIzaSyA0qX3ixQArOBOKLK4ZE_OkivhVvY2l1us",
authDomain: "crop-shield-1b392.firebaseapp.com",
databaseURL: "https://crop-shield-1b392-default-rtdb.asia-southeast1.firebasedatabase.app",
projectId: "crop-shield-1b392",
storageBucket: "crop-shield-1b392.firebasestorage.app",
messagingSenderId: "244726820699",
appId: "1:244726820699:web:86f1410ec94c2fed12f47e"
};

const app=initializeApp(firebaseConfig);
const database=getDatabase(app);

// This function is available to the dashboard script
window.saveToFirebase=async function(data){
try{
await set(ref(database,"sensorReadings/latest"),data);
console.log("Dashboard readings saved to Firebase!",data);
}catch(error){
console.error("Firebase save error:",error);
}
};

// Connection test
set(ref(database,"connectionTest"),{
message:"CropShield Firebase working"
})
.then(()=>console.log("Data saved successfully!"))
.catch(error=>console.error("Firebase error:",error));

console.log("Firebase database is ready!");
console.log("CropShield Firebase Connected!");
</script>

</body>
</html>
