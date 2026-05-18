<!DOCTYPE html>
<html lang="pt-br">

<head>

<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Comercial Ultralight</title>

<style>

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family:Arial,sans-serif;
}

body{
  background:#050505;
  color:white;
  overflow:hidden;
}

.container{
  display:flex;
  height:100vh;
  gap:20px;
  padding:20px;
}

.left,
.right{
  width:23%;
  display:flex;
  flex-direction:column;
  gap:20px;
}

.center{
  flex:1;
  background:#101010;
  border-radius:20px;
  display:flex;
  flex-direction:column;
  justify-content:center;
  align-items:center;
  position:relative;
}

.card{
  background:#101010;
  border-radius:20px;
  padding:20px;
}

.card h2{
  margin-bottom:15px;
  font-size:24px;
}

.ranking-item{
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:12px;
  border-radius:12px;
  background:#181818;
  margin-bottom:10px;
  font-size:20px;
}

.timer{
  font-size:130px;
  font-weight:bold;
  color:#00ff95;
}

.alert{
  color:red;
  animation:piscar 1s infinite;
}

@keyframes piscar{
  50%{
    opacity:0.3;
  }
}

.message{
  position:absolute;
  bottom:40px;
  width:100%;
  text-align:center;
  font-size:42px;
  color:#888;
  padding:0 30px;
  font-weight:bold;
}

.meta{
  font-size:55px;
  color:#00ff95;
  font-weight:bold;
}

.button-grid{
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:10px;
}

button{
  padding:14px;
  border:none;
  border-radius:10px;
  background:#00ff95;
  color:black;
  font-size:17px;
  font-weight:bold;
  cursor:pointer;
}

.warning{
  background:#ffb300;
}

.danger{
  background:red;
  color:white;
}

input{
  border:none;
  border-radius:10px;
  background:#181818;
  color:#00ff95;
  padding:10px;
  font-size:20px;
  font-weight:bold;
  outline:none;
}

.percent-input{
  width:90px;
  text-align:center;
}

.meta-title{
  width:100%;
  background:#181818;
  color:red;
  text-align:center;
}

#ultimaLigacao{
  position:absolute;
  top:40px;
  font-size:28px;
  color:#00ff95;
  font-weight:bold;
}

</style>

</head>

<body>

<div class="container">

  <!-- ESQUERDA -->

  <div class="left">

    <div class="card">

      <h2>🏆 Ranking Diário</h2>

      <div id="ranking"></div>

    </div>

    <div class="card">

      <h2>💰 Metas</h2>

      <input
        id="tituloMeta"
        class="meta-title"
        value="META IDEAL DE VENDAS"
      >

      <br><br>

      <div class="ranking-item">

        <span>Controle de Pragas</span>

        <input
          type="number"
          id="controle"
          class="percent-input"
          value="0"
          min="0"
        >

      </div>

      <div class="ranking-item">

        <span>Patrick</span>

        <input
          type="number"
          id="patrick"
          class="percent-input"
          value="0"
          min="0"
        >

      </div>

      <div class="ranking-item">

        <span>Bruna</span>

        <input
          type="number"
          id="bruna"
          class="percent-input"
          value="0"
          min="0"
        >

      </div>

      <div class="ranking-item">

        <span>Nayara</span>

        <input
          type="number"
          id="nayara"
          class="percent-input"
          value="0"
          min="0"
        >

      </div>

    </div>

  </div>

  <!-- CENTRO -->

  <div class="center">

    <div id="ultimaLigacao">
      Última ligação: —
    </div>

    <div class="timer" id="timer">
      00:00
    </div>

    <div class="message" id="message">
      Os clientes não vão ligar sozinhos.
    </div>

  </div>

  <!-- DIREITA -->

  <div class="right">

    <div class="card">

      <h2>📞 Ligações de Hoje</h2>

      <div class="meta" id="total">
        0
      </div>

    </div>

    <div class="card">

      <h2>🎯 Meta</h2>

      <div class="meta">
        50
      </div>

    </div>

    <div class="card">

      <h2>🎮 Controle</h2>

      <div class="button-grid">

        <button onclick="ligar('Solange')">Sol</button>
        <button onclick="ligar('Lucas')">Lucas</button>

        <button onclick="ligar('Patrick')">Patrick</button>
        <button onclick="ligar('Bruna')">Bruna</button>

        <button onclick="ligar('Nayara')">Nayara</button>
        <button onclick="ligar('Enrico')">Enrico</button>

        <button class="warning" onclick="maisTempo()">+</button>
        <button class="warning" onclick="menosTempo()">-</button>

        <button class="danger" onclick="resetarTimer()">
          Timer
        </button>

        <button class="danger" onclick="resetarRanking()">
          Diário
        </button>

      </div>

    </div>

  </div>

</div>

<audio id="som">
  <source src="https://actions.google.com/sounds/v1/alarms/alarm_clock.ogg">
</audio>

<script>

const frases = [

  "Doeu ligar para o cliente?",
  "O telefone não morde.",
  "A meta não bate sozinha.",
  "Silêncio demais pra equipe comercial.",
  "Mais ligação = mais venda.",
  "O teclado não fecha venda.",
  "Cliente não responde? Liga.",
  "WhatsApp não fecha tudo.",
  "Bora movimentar esse comercial.",
  "Sem ligação sem negociação."

];

let vendedores = JSON.parse(
  localStorage.getItem("vendedores")
) || {

  Solange:{d:0},
  Lucas:{d:0},
  Patrick:{d:0},
  Bruna:{d:0},
  Nayara:{d:0},
  Enrico:{d:0}

};

let total =
  parseInt(localStorage.getItem("total")) || 0;

let segundos =
  parseInt(localStorage.getItem("segundos")) || 0;

document.getElementById("total").innerText = total;

document.getElementById("tituloMeta").value =
  localStorage.getItem("tituloMeta")
  || "META IDEAL DE VENDAS";

document.getElementById("controle").value =
  localStorage.getItem("controle") || 0;

document.getElementById("patrick").value =
  localStorage.getItem("patrick") || 0;

document.getElementById("bruna").value =
  localStorage.getItem("bruna") || 0;

document.getElementById("nayara").value =
  localStorage.getItem("nayara") || 0;

function salvar(){

  localStorage.setItem(
    "vendedores",
    JSON.stringify(vendedores)
  );

  localStorage.setItem("total",total);

  localStorage.setItem("segundos",segundos);

  localStorage.setItem(
    "tituloMeta",
    document.getElementById("tituloMeta").value
  );

  localStorage.setItem(
    "controle",
    document.getElementById("controle").value
  );

  localStorage.setItem(
    "patrick",
    document.getElementById("patrick").value
  );

  localStorage.setItem(
    "bruna",
    document.getElementById("bruna").value
  );

  localStorage.setItem(
    "nayara",
    document.getElementById("nayara").value
  );

}

function atualizarRanking(){

  let html = "";

  Object.entries(vendedores)

  .sort((a,b)=>b[1].d-a[1].d)

  .forEach((v,i)=>{

    html += `

      <div class="ranking-item">

        <span>${i+1}. ${v[0]}</span>

        <span>${v[1].d}</span>

      </div>

    `;

  });

  document.getElementById("ranking").innerHTML = html;

}

function ligar(nome){

  vendedores[nome].d++;

  let agora = new Date();

  let hora =
    agora.getHours().toString().padStart(2,"0");

  let minuto =
    agora.getMinutes().toString().padStart(2,"0");

  document.getElementById("ultimaLigacao").innerText =
    `Última ligação: ${nome} • ${hora}:${minuto}`;

  total++;

  document.getElementById("total").innerText =
    total;

  atualizarRanking();

  segundos = 0;

  document.getElementById("message").innerText =
    frases[
      Math.floor(Math.random()*frases.length)
    ];

  salvar();

}

function atualizarTimer(){

  segundos++;

  let m =
    String(Math.floor(segundos/60))
    .padStart(2,"0");

  let s =
    String(segundos%60)
    .padStart(2,"0");

  document.getElementById("timer").innerText =
    `${m}:${s}`;

  if(segundos >= 1200){

    document.getElementById("timer")
      .classList.add("alert");

    document.getElementById("message").innerText =
      "⚠ SEM LIGAÇÕES HÁ 20 MINUTOS ⚠";

    document.getElementById("som").play();

  }

  else{

    document.getElementById("timer")
      .classList.remove("alert");

  }

  salvar();

}

function maisTempo(){

  segundos += 60;

}

function menosTempo(){

  segundos -= 60;

  if(segundos < 0){
    segundos = 0;
  }

}

function resetarTimer(){

  segundos = 0;

}

function resetarRanking(){

  for(let n in vendedores){

    vendedores[n].d = 0;

  }

  total = 0;

  document.getElementById("total").innerText = 0;

  atualizarRanking();

  salvar();

}

setInterval(atualizarTimer,1000);

document.getElementById("tituloMeta")
.addEventListener("input",salvar);

document.getElementById("controle")
.addEventListener("input",salvar);

document.getElementById("patrick")
.addEventListener("input",salvar);

document.getElementById("bruna")
.addEventListener("input",salvar);

document.getElementById("nayara")
.addEventListener("input",salvar);

atualizarRanking();

</script>

</body>

</html>
