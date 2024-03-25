<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Rastreamento de Localização por Placa de Veículo</title>
<style>
/* Estilo do botão */
.custom-button {
    display: inline-block;
    padding: 10px 20px;
    font-size: 16px;
    font-weight: bold;
    text-align: center;
    text-decoration: none;
    color: #fff;
    background-color: #007bff;
    border: 2px solid #007bff;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s, border-color 0.3s, color 0.3s;
}

/* Estilo do botão quando passa o mouse */
.custom-button:hover {
    background-color: #0056b3;
    border-color: #0056b3;
}

/* Estilo do botão quando está pressionado */
.custom-button:active {
    background-color: #003d80;
    border-color: #003d80;
}

/* Estilo dos campos de entrada e rótulos */
.input-container {
    margin-bottom: 10px;
}
.input-container label {
    display: block;
    margin-bottom: 5px;
}

/* Estilo da tabela de dados */
#dados-table {
    border-collapse: collapse;
    width: 100%;
}
#dados-table th, #dados-table td {
    border: 1px solid #dddddd;
    padding: 8px;
    text-align: left;
}
#dados-table th {
    background-color: #f2f2f2;
}
</style>
<script>
var intervaloAtualizacao; // Variável para armazenar o identificador do intervalo de atualização
var dados = {}; // Objeto para armazenar os dados de localização por placa de veículo

function iniciarAtualizacaoAutomatica() {
    // Configura a atualização automática para ocorrer a cada 5 segundos (5000 milissegundos)
    intervaloAtualizacao = setInterval(getLocation, 5000);
}

function getLocation() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(sendLocation);
    } else {
        alert("Geolocalização não é suportada pelo seu navegador.");
    }
}

function sendLocation(position) {
    // Extrair a latitude e longitude da posição
    var latitude = position.coords.latitude;
    var longitude = position.coords.longitude;

    // Obter a placa do veículo
    var placa = document.getElementById("placa").value.toUpperCase();

    // Verificar se o campo da placa está preenchido
    if (placa === "") {
        alert("Por favor, preencha o campo da placa do veículo.");
        return;
    }

    // Armazenar a nova localização sob a placa do veículo
    dados[placa] = { latitude: latitude, longitude: longitude, dataHora: new Date().toLocaleString() };

    // Atualizar a tabela de dados
    atualizarTabela();
}

function pararAtualizacaoAutomatica() {
    clearInterval(intervaloAtualizacao);
    console.log("Atualização automática interrompida.");
}

function abrirGoogleMaps(latitude, longitude) {
    var url = "https://www.google.com/maps?q=" + latitude + "," + longitude;
    window.open(url, "_blank");
}

function atualizarTabela() {
    var tabela = document.getElementById("dados-table");
    var tbody = tabela.getElementsByTagName('tbody')[0];

    // Limpar o conteúdo da tabela
    tbody.innerHTML = "";

    // Percorrer os dados e adicionar as entradas na tabela
    for (var placa in dados) {
        if (dados.hasOwnProperty(placa)) {
            var entrada = dados[placa];
            var tr = document.createElement("tr");
            var tdPlaca = document.createElement("td");
            tdPlaca.innerText = placa;
            var tdLatitude = document.createElement("td");
            tdLatitude.innerText = entrada.latitude;
            var tdLongitude = document.createElement("td");
            tdLongitude.innerText = entrada.longitude;
            var tdDataHora = document.createElement("td");
            tdDataHora.innerText = entrada.dataHora;
            var tdLinkMapa = document.createElement("td");
            var mapaLink = document.createElement("button");
            mapaLink.textContent = 'Ver no Google Maps';
            mapaLink.onclick = function() {
                abrirGoogleMaps(entrada.latitude, entrada.longitude);
            };
            tdLinkMapa.appendChild(mapaLink);

            tr.appendChild(tdPlaca);
            tr.appendChild(tdLatitude);
            tr.appendChild(tdLongitude);
            tr.appendChild(tdDataHora);
            tr.appendChild(tdLinkMapa);

            tbody.appendChild(tr);
        }
    }
}

// Evento de escuta para converter a entrada da placa em maiúsculas
document.getElementById("placa").addEventListener("input", function() {
    this.value = this.value.toUpperCase();
});
</script>
</head>
<body>

<!-- Campo de entrada para a placa do veículo -->
<div class="input-container">
    <label for="placa">Placa do Veículo:</label>
    <input type="text" id="placa" name="placa">
</div>

<!-- Botões para iniciar e parar a atualização automática -->
<button class="custom-button" onclick="iniciarAtualizacaoAutomatica()">Iniciar Atualização Automática</button>
<button class="custom-button" onclick="pararAtualizacaoAutomatica()">Parar Atualização Automática</button>

<!-- Tabela para exibir os dados de localização -->
<table id="dados-table">
    <thead>
        <tr>
            <th>Placa do Veículo</th>
            <th>Latitude</th>
            <th>Longitude</th>
            <th>Data e Hora da Última Atualização</th>
            <th>Google Maps</th>
        </tr>
    </thead>
    <tbody></tbody>
</table>

</body>
</html>
