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

// 1️⃣ Lista completa de aeroportos [lat, lng]

// --- Brasil (~70 aeroportos para rotas domésticas) ---
const airports = {
  "SSA": [-12.908611, -38.322500], "CGH": [-23.626667, -46.656111],
  "ILH": [-14.793611, -39.047222], "BSB": [-15.869722, -47.920833],
  "GIG": [-22.809, -43.250], "SDU": [-22.910833, -43.163056],
  "POA": [-29.993889, -51.171389], "FOR": [-3.776944, -38.532222],
  "BEL": [-1.378333, -48.476667], "REC": [-8.126667, -34.923333],
  "VCP": [-23.0075, -47.1344], "CWB": [-25.528889, -49.175278],
  "MAO": [-3.038056, -59.984444], "NAT": [-5.846944, -35.2075],
  "CGB": [-15.645, -56.116], "CNF": [-19.6244, -43.9719], "MCZ": [-9.5106, -35.7914],
  "JOI": [-27.6647, -48.5494], "SSA": [-12.908611, -38.322500]
  // Continue adicionando todos os aeroportos do Brasil para completar 70
};

// --- América do Sul (~150 rotas) ---
airports["BOG"] = [4.70159, -74.1469]; airports["LIM"] = [-12.0219, -77.1143];
airports["EZE"] = [-34.812222, -58.5375]; airports["SCL"] = [-33.393, -70.7858];
airports["UIO"] = [-0.129167, -78.3575]; airports["GYE"] = [-2.157778, -79.883611];
airports["SLA"] = [-24.783611, -65.423056]; airports["LPB"] = [-16.513056, -68.192222];
airports["ASU"] = [-25.239167, -57.519444]; airports["CAY"] = [4.9383, -52.3608];
// Continue adicionando até completar todos os aeroportos da América do Sul (~150)

// --- Europa (~89 rotas) ---
airports["LHR"] = [51.4700, -0.4543]; airports["CDG"] = [49.0097, 2.5479];
airports["FRA"] = [50.0379, 8.5622]; airports["MAD"] = [40.4983, -3.5676];
airports["BCN"] = [41.2974, 2.0833]; airports["MXP"] = [45.6306, 8.7281];
airports["AMS"] = [52.3105, 4.7683]; airports["FCO"] = [41.8003, 12.2389];
airports["DUB"] = [53.4213, -6.2701]; airports["LGW"] = [51.1537, -0.1821];
airports["STN"] = [51.885, 0.235]; airports["ORY"] = [48.7233, 2.3799];
airports["BRU"] = [50.9014, 4.4844]; airports["ZRH"] = [47.4647, 8.5492];
airports["VIE"] = [48.1103, 16.5697]; airports["CPH"] = [55.6181, 12.6562];
airports["OSL"] = [60.1939, 11.1004]; airports["ARN"] = [59.6519, 17.9186];
airports["HEL"] = [60.3172, 24.9633]; airports["ATH"] = [37.9364, 23.9445];
airports["MUC"] = [48.3538, 11.7861]; airports["TXL"] = [52.5597, 13.2877];
airports["BER"] = [52.3667, 13.5033]; airports["DUS"] = [51.2895, 6.7668];
// Continue adicionando até completar 89 aeroportos europeus

// --- América do Norte (~65 rotas totais) ---
airports["MIA"] = [25.7959, -80.2870]; airports["NYC"] = [40.6413, -73.7781];
airports["LAX"] = [33.9416, -118.4085]; airports["YYZ"] = [43.6777, -79.6248];
airports["ORD"] = [41.9742, -87.9073]; airports["DFW"] = [32.8998, -97.0403];
airports["IAD"] = [38.9531, -77.4565]; airports["ATL"] = [33.6407, -84.4277];
airports["BOS"] = [42.3656, -71.0096]; airports["SEA"] = [47.449, -122.309];
airports["SFO"] = [37.6213, -122.379]; airports["LAS"] = [36.0801, -115.1522];
airports["DEN"] = [39.8617, -104.6731]; airports["PHX"] = [33.4342, -112.0116];
airports["MCO"] = [28.4312, -81.3081]; airports["CLT"] = [35.214, -80.9431];
// Continue adicionando os outros aeroportos da América do Norte

// --- Ásia (~5 rotas) ---
airports["NRT"] = [35.7719, 140.3929]; airports["HND"] = [35.5494, 139.7798];
airports["SIN"] = [1.3644, 103.9915]; airports["DXB"] = [25.2532, 55.3657];
airports["HKG"] = [22.308, 113.918];

// 2️⃣ Lista de rotas [origem, destino]
// Adicione todas as rotas da Zenith, Brasil, América do Sul, Europa, América do Norte, Ásia
// Exemplo de rotas principais (o resto você pode continuar distribuindo):

const routes = [
  // Brasil
  ["SSA","CGH"],["SSA","ILH"],["SSA","BSB"],["SSA","GIG"],["SSA","POA"],["SSA","REC"],
  ["SSA","FOR"],["SSA","BEL"],["CGH","BSB"],["CGH","POA"],["CGH","REC"],["BSB","POA"],
  ["BSB","GIG"],["GIG","SDU"],["POA","CWB"],["CWB","VCP"],["CWB","MAO"],["REC","NAT"],
  // América do Sul
  ["SSA","BOG"],["SSA","LIM"],["SSA","EZE"],["SSA","SCL"],["SSA","UIO"],["SSA","GYE"],
  ["BOG","LIM"],["BOG","EZE"],["LIM","EZE"],["SCL","EZE"],["UIO","GYE"],["SLA","LPB"],["ASU","BOG"],
  // Europa
  ["SSA","LHR"],["SSA","CDG"],["SSA","FRA"],["SSA","MAD"],["SSA","BCN"],["SSA","MXP"],
  ["SSA","AMS"],["SSA","FCO"],["SSA","DUB"],["SSA","LGW"],["SSA","STN"],["SSA","ORY"],
  ["SSA","BRU"],["SSA","ZRH"],["SSA","VIE"],["SSA","CPH"],["SSA","OSL"],["SSA","ARN"],
  ["SSA","HEL"],["SSA","ATH"],["SSA","MUC"],["SSA","TXL"],["SSA","BER"],["SSA","DUS"],
  // América do Norte
  ["SSA","MIA"],["SSA","NYC"],["SSA","LAX"],["SSA","YYZ"],["SSA","ORD"],["SSA","DFW"],
  ["SSA","IAD"],["SSA","ATL"],["SSA","BOS"],["SSA","SEA"],["SSA","SFO"],["SSA","LAS"],
  ["SSA","DEN"],["SSA","PHX"],["SSA","MCO"],["SSA","CLT"],["NYC","MIA"],["NYC","LAX"],
  // Ásia
  ["SSA","NRT"],["SSA","HND"],["SSA","SIN"],["SSA","DXB"],["SSA","HKG"]
  // Continue adicionando todas as outras rotas até chegar a ~322 rotas
];

// 3️⃣ Adiciona marcadores e linhas no mapa
routes.forEach(route => {
  const from = airports[route[0]];
  const to = airports[route[1]];

  if(from && to){ // garante que só usa rotas válidas
    L.marker(from).addTo(map).bindPopup(route[0]);
    L.marker(to).addTo(map).bindPopup(route[1]);

    L.polyline([from, to], {
      color: '#00cfff',
      weight: 3,
      opacity: 0.9
    }).addTo(map);
  }
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
