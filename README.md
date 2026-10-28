# jarvis-iphone
Mi propio asistente personal JARVIS


<!DOCTYPE html>

<html lang="es">

<head>

<meta charset="UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1">

<meta name="theme-color" content="#050b16">

<title>JARVIS AI</title>

<style>

*{box-sizing:border-box}

body{

  margin:0;background:#050b16;color:#b9f5ff;

  font-family:-apple-system,BlinkMacSystemFont,sans-serif;

  min-height:100vh;text-align:center;

  background-image:radial-gradient(ellipse at 50% 35%,

  #103047 0%,#081522 38%,#050b16 75%);

}

header{padding:26px 12px 8px;letter-spacing:5px}

h1{font-size:30px;margin:8px 0;color:#e1fcff}

.small{font-size:11px;letter-spacing:3px;color:#48cce8}

#clock{font-size:14px;color:#71cde0;margin:18px}

.core{

 width:220px;height:220px;border-radius:50%;

 margin:28px auto;display:grid;place-items:center;

 border:2px solid #43d9f6;

 box-shadow:0 0 22px #147da0, inset 0 0 24px #147da044;

 position:relative;

}

.core:before,.core:after{

 content:"";position:absolute;border-radius:50%;

 border:1px solid #3ab8df77;

}

.core:before{inset:12px}

.core:after{inset:25px;border-style:dashed}

#orb{

 width:90px;height:90px;border-radius:50%;

 background:radial-gradient(circle,#d6ffff 0%,#43d9f6 15%,

 #126b9c 45%,#061522 72%);

 box-shadow:0 0 35px #37d8ff;

}

.listening{animation:pulse 1s infinite alternate}

@keyframes pulse{

 to{box-shadow:0 0 65px #42eaff;transform:scale(1.08)}

}

#status{font-size:12px;letter-spacing:2px;color:#72d8ec}

#answer{

 margin:22px auto;padding:0 22px;max-width:500px;

 min-height:54px;line-height:1.6;color:#e1faff;

}

.panel{max-width:500px;margin:auto;padding:16px}

input{

 width:100%;padding:15px;border-radius:10px;

 border:1px solid #23667b;background:#081522;

 color:white;font-size:16px;outline:none;

}

button{

 padding:13px 15px;border-radius:9px;

 border:1px solid #2a9eb9;background:#0b2637;

 color:#b9f5ff;font-size:14px;

}

button:active{background:#14506a}

.controls{display:flex;gap:8px;margin-top:10px}

.controls button{flex:1}

.tiles{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:18px}

.tile{padding:16px 8px;line-height:1.7}

.tile b{display:block;font-size:12px;letter-spacing:1px}

footer{padding:28px;color:#558493;font-size:11px}

a{color:#66dff7}

</style>

</head>

<body>

<header>

  <div class="small">STARK INDUSTRIES // PERSONAL AI</div>

  <h1>J.A.R.V.I.S.</h1>

  <div class="small">JUST A RATHER VERY INTELLIGENT SYSTEM</div>

</header>

<div id="clock">INICIALIZANDO...</div>

<div class="core">

  <div id="orb"></div>

</div>

<div id="status">SISTEMA PREPARADO</div>

<div id="answer">A tu servicio. Pulsa el micrófono o escribe una orden.</div>

<div class="panel">

  <input id="command" placeholder="Dime qué necesitas, jefe..."

  autocomplete="off">

  <div class="controls">

    <button onclick="listen()">🎙 HABLAR</button>

    <button onclick="send()">➤ ENVIAR</button>

  </div>

  <div class="tiles">

    <button class="tile" onclick="runCommand('qué hora es')">

      ◷<b>HORA ACTUAL</b>

    </button>

    <button class="tile" onclick="runCommand('qué recuerdas')">

      ◈<b>MI MEMORIA</b>

    </button>

    <button class="tile" onclick="runCommand('abre YouTube')">

      ▷<b>YOUTUBE</b>

    </button>

    <button class="tile" onclick="runCommand('ayuda')">

      ⌘<b>COMANDOS</b>

    </button>

  </div>

</div>

<footer>

  JARVIS PERSONAL v1.0 · VOZ · MEMORIA LOCAL<br>

  Los datos guardados permanecen en este navegador.

</footer>

<script>

const $ = id => document.getElementById(id);

const memoryKey = "jarvis_memory_v1";

let memory = JSON.parse(localStorage.getItem(memoryKey) || "[]");

function clock(){

  $("clock").textContent = new Date().toLocaleString("es-ES",{

    weekday:"long",day:"numeric",month:"long",

    hour:"2-digit",minute:"2-digit"

  }).toUpperCase();

}

clock();

setInterval(clock,15000);

function speak(text){

  if(!("speechSynthesis" in window)) return;

  speechSynthesis.cancel();

  const msg = new SpeechSynthesisUtterance(text);

  msg.lang = "es-ES";

  msg.rate = 0.94;

  msg.pitch = 0.85;

  speechSynthesis.speak(msg);

}

function reply(text){

  $("answer").textContent = text;

  speak(text);

  $("status").textContent = "SISTEMA ACTIVO";

}

function saveMemory(text){

  memory.push({text, date:new Date().toLocaleDateString("es-ES")});

  localStorage.setItem(memoryKey,JSON.stringify(memory));

}

function runCommand(raw){

  const q = raw.trim();

  const s = q.toLowerCase();

  if(!q) return;

  $("command").value = q;

  $("status").textContent = "PROCESANDO ORDEN...";

  if(s.startsWith("recuerda ")){

    const note = q.slice(9).trim();

    if(!note){reply("No he encontrado nada que guardar.");return;}

    saveMemory(note);

    reply("Entendido. He guardado en mi memoria: "+note);

  }

  else if(s.includes("qué recuerdas") ||

          s.includes("que recuerdas") ||

          s.includes("mi memoria")){

    reply(memory.length

      ? "Esto es lo que recuerdo: "+memory.map((m,i)=>

          (i+1)+". "+m.text).join(". ")

      : "Todavía no tengo recuerdos guardados. Di: recuerda seguido de una nota.");

  }

  else if(s.startsWith("olvida todo")){

    reply("Para borrar la memoria, pulsa el botón de borrar que aparece abajo.");

    showDelete();

  }

  else if(s.includes("qué hora") || s.includes("que hora")){

    reply("Son las "+new Date().toLocaleTimeString("es-ES",

      {hour:"2-digit",minute:"2-digit"})+".");

  }

  else if(s.includes("qué día") || s.includes("que dia") ||

          s.includes("fecha de hoy")){

    reply("Hoy es "+new Date().toLocaleDateString("es-ES",

      {weekday:"long",day:"numeric",month:"long",year:"numeric"})+".");

  }

  else if(s.includes("abre youtube")){

    reply("Abriendo YouTube.");

    window.open("https://www.youtube.com/","_blank");

  }

  else if(s.includes("abre google")){

    reply("Abriendo Google.");

    window.open("https://www.google.com/","_blank");

  }

  else if(s.includes("abre mapas") || s.includes("abre google maps")){

    reply("Abriendo mapas.");

    window.open("https://maps.google.com/","_blank");

  }

  else if(s.includes("ayuda") || s.includes("qué puedes hacer")){

    reply("Puedo decirte la hora y la fecha, guardar recuerdos, consultar mi memoria y abrir YouTube, Google o mapas. Todavía no estoy conectado a un modelo de inteligencia artificial.");

  }

  else{

    reply("He recibido tu orden: "+q+

      ". Todavía necesito conectar un modelo de inteligencia artificial para responder preguntas complejas.");

  }

}

function send(){

  runCommand($("command").value);

}

$("command").addEventListener("keydown",e=>{

  if(e.key==="Enter") send();

});

function listen(){

  const SR = window.SpeechRecognition ||

             window.webkitSpeechRecognition;

  if(!SR){

    reply("El reconocimiento de voz no está disponible en este navegador. Puedes escribir tu orden.");

    return;

  }

  const rec = new SR();

  rec.lang = "es-ES";

  rec.interimResults = false;

  rec.maxAlternatives = 1;

  $("status").textContent = "TE ESTOY ESCUCHANDO...";

  $("orb").classList.add("listening");

  rec.onresult = e=>{

    const words = e.results[0][0].transcript;

    $("command").value = words;

    runCommand(words);

  };

  rec.onerror = ()=>{

    $("status").textContent = "VOZ NO DISPONIBLE";

    $("answer").textContent =

      "No he podido reconocer la voz. Prueba otra vez o escribe.";

  };

  rec.onend = ()=> $("orb").classList.remove("listening");

  try{rec.start();}

  catch(e){

    $("orb").classList.remove("listening");

    reply("No he podido iniciar el micrófono. Inténtalo de nuevo.");

  }

}

function showDelete(){

  if($("deleteMemory")) return;

  const b=document.createElement("button");

  b.id="deleteMemory";

  b.textContent="BORRAR TODA LA MEMORIA";

  b.style.margin="8px";

  b.onclick=()=>{

    if(confirm("¿Seguro que quieres borrar todos los recuerdos?")){

      memory=[];

      localStorage.removeItem(memoryKey);

      b.remove();

      reply("Memoria borrada.");

    }

  };

  document.querySelector(".panel").appendChild(b);

}

</script>

</body>

</html
