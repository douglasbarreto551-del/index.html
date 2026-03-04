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

// 1️⃣ Lista de aeroportos com coordenadas [lat, lng]
const airports = {
  "SSA": [-12.908611, -38.322500],
  "CGH": [-23.626667, -46.656111],
  "ILH": [-14.793611, -39.047222],
  "BSB": [-15.869722, -47.920833],
  "GIG": [-22.809, -43.250],
  "SDU": [-22.910833, -43.163056],
  "POA": [-29.993889, -51.171389],
  "FOR": [-3.776944, -38.532222],
  "BEL": [-1.378333, -48.476667],
  "REC": [-8.126667, -34.923333],
  "VCP": [-23.0075, -47.1344],
  "CWB": [-25.528889, -49.175278],
  "MAO": [-3.038056, -59.984444],
  "NAT": [-5.846944, -35.2075],
  "BOG": [4.70159, -74.1469],
  "LIM": [-12.0219, -77.1143],
  "EZE": [-34.812222, -58.5375],
  "SCL": [-33.393, -70.7858],
  "UIO": [-0.129167, -78.3575],
  "GYE": [-2.157778, -79.883611],
  "LHR": [51.4700, -0.4543],
  "CDG": [49.0097, 2.5479],
  "FRA": [50.0379, 8.5622],
  "MAD": [40.4983, -3.5676],
  "BCN": [41.2974, 2.0833],
  "MXP": [45.6306, 8.7281],
  "AMS": [52.3105, 4.7683],
  "MIA": [25.7959, -80.2870],
  "NYC": [40.6413, -73.7781],
  "LAX": [33.9416, -118.4085],
  "YYZ": [43.6777, -79.6248],
  "NRT": [35.7719, 140.3929],
  "HND": [35.5494, 139.7798],
  "SIN": [1.3644, 103.9915],
  "DXB": [25.2532, 55.3657],
  "HKG": [22.308, 113.918]
};

// 2️⃣ Lista de rotas [origem, destino]
const routes = [
  // Brasil
  ["SSA","CGH"],["SSA","ILH"],["SSA","BSB"],["SSA","GIG"],["SSA","POA"],["SSA","REC"],
  ["SSA","FOR"],["SSA","BEL"],["CGH","BSB"],["CGH","POA"],["CGH","REC"],
  ["BSB","POA"],["BSB","GIG"],["GIG","SDU"],["POA","CWB"],["CWB","VCP"],
  // América do Sul
  ["SSA","BOG"],["SSA","LIM"],["SSA","EZE"],["SSA","SCL"],["SSA","UIO"],["SSA","GYE"],
  ["BOG","LIM"],["BOG","EZE"],["LIM","EZE"],["SCL","EZE"],["UIO","GYE"],
  // Europa
  ["SSA","LHR"],["SSA","CDG"],["SSA","FRA"],["SSA","MAD"],["SSA","BCN"],["SSA","MXP"],
  ["SSA","AMS"],
  // América do Norte
  ["SSA","MIA"],["SSA","NYC"],["SSA","LAX"],["SSA","YYZ"],
  // Ásia
  ["SSA","NRT"],["SSA","HND"],["SSA","SIN"],["SSA","DXB"],["SSA","HKG"]
];

// 3️⃣ Criar marcadores e linhas
routes.forEach(route => {
  const from = airports[route[0]];
  const to = airports[route[1]];

  // Marcadores
  L.marker(from).addTo(map).bindPopup(route[0]);
  L.marker(to).addTo(map).bindPopup(route[1]);

  // Linha azul neon
  L.polyline([from, to], {
    color: '#00cfff',
    weight: 3,
    opacity: 0.9
  }).addTo(map);
});

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
