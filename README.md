<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Zenith Airways | Network</title>

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<style>
body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: linear-gradient(135deg, #0a192f, #10273d, #1b3b5a);
}

header{
padding:20px;
text-align:center;
font-size:26px;
background:linear-gradient(90deg,#07121d,#10273d);
letter-spacing:2px;
}

#map{
height:90vh;
}

.panel{
padding:15px;
background:#0f2236;
}

input,button{
padding:10px;
margin:5px;
border:none;
border-radius:6px;
}

button{
background:#ff2f7d;
color:white;
}
</style>
</head>

<body>

<header>ZENITH AIRWAYS — GLOBAL ROUTE NETWORK</header>

<nav style="text-align:center; padding:20px; background:#10273d;">
<a href="index.html" style="color:#00cfff; font-weight:bold; margin:0 20px; text-decoration:none;">Network</a>
<a href="fleet.html" style="color:#00cfff; font-weight:bold; margin:0 20px; text-decoration:none;">Fleet & Operations</a>
</nav>

<div id="map"></div>

<div class="panel">
<h3>Adicionar rota (Admin)</h3>

<input id="from" placeholder="Origem">
<input id="to" placeholder="Destino">
<button onclick="addRoute()">Adicionar</button>

<div id="routes"></div>
</div>

<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

<script>
const map = L.map('map').setView([-14, -55], 4);

L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '&copy; OpenStreetMap contributors'
}).addTo(map);

// 👇 LINHAS DE ROTAS AQUI
var salvador = [-12.908611, -38.322500];
var bogota = [4.70159, -74.1469];

L.marker(salvador).addTo(map).bindPopup("Salvador (SSA)");
L.marker(bogota).addTo(map).bindPopup("Bogotá (BOG)");

L.polyline([salvador, bogota], {
    color: '#00cfff',
    weight: 4,
    opacity: 0.9
}).addTo(map);

let routes=[];

function addRoute(){

const from=document.getElementById("from").value;
const to=document.getElementById("to").value;

if(!from||!to)return;

routes.push({from,to});

updateList();
}

function updateList(){
const div=document.getElementById("routes");
div.innerHTML="";

routes.forEach(r=>{
div.innerHTML+=`✈ ${r.from} → ${r.to}<br>`;
});
}
</script>

</body>
</html>
