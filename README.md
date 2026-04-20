# Harry.cr-er-sort.com
créer ton sort de Poudlard 
<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Magic MMO FINAL</title>

<style>
body{
  margin:0;
  font-family:Arial;
  background:radial-gradient(circle,#0a0a2a,#000);
  color:white;
  text-align:center;
}

input,button,select{
  width:80%;
  padding:10px;
  margin:6px;
  border-radius:10px;
  border:none;
}

button{
  background:gold;
  font-weight:bold;
  cursor:pointer;
}

#app{display:none;}

.card{
  background:rgba(255,255,255,0.08);
  margin:6px;
  padding:8px;
  border-radius:10px;
}

#avatar{
  width:120px;
  height:200px;
  margin:auto;
  position:relative;
}

.part{
  position:absolute;
  left:50%;
  transform:translateX(-50%);
}

#head{width:50px;height:50px;top:0;background:#ffcc99;}
#body{width:70px;height:80px;top:55px;background:#666;}
#legs{width:60px;height:70px;top:140px;background:#444;}

#chat{
  height:150px;
  overflow:auto;
  border:1px solid white;
  margin:10px;
}
</style>
</head>

<body>

<!-- LOGIN -->
<div id="login">
<h1>🧙 Magic MMO</h1>
<input id="name" placeholder="Pseudo">
<button onclick="start()">Entrer</button>
</div>

<!-- GAME -->
<div id="app">

<h2>Bienvenue <span id="user"></span></h2>

<div id="avatar">
  <div id="head" class="part"></div>
  <div id="body" class="part"></div>
  <div id="legs" class="part"></div>
</div>

<p>❤️ HP: <span id="hp">100</span></p>

<button onclick="loadPlayers()">🌍 Joueurs</button>
<div id="players"></div>

<h3>⚔️ Combat</h3>
<button onclick="attack()">Attaquer</button>
<p>💀 Ennemi HP: <span id="enemy">100</span></p>

<h3>🪄 Sorts</h3>
<input id="spellName" placeholder="Nom sort">
<select id="spellElement">
  <option>Feu</option>
  <option>Glace</option>
  <option>Éclair</option>
  <option>Vent</option>
  <option>Ténèbres</option>
</select>
<input id="spellPower" type="number" placeholder="Max 59">
<button onclick="createSpell()">Créer sort</button>

<h3>📘 Grimoire</h3>
<div id="spells"></div>

<h3>💬 Chat</h3>
<div id="chat"></div>
<input id="msg" placeholder="Message">
<button onclick="sendMsg()">Envoyer</button>

<button onclick="save()">💾 Sauvegarder</button>

</div>

<script>

//////////////////////////////
// STATE
//////////////////////////////
let user="";
let hp=100;
let enemy=100;

let spells=[];
let equipped=[];
let players=["Merlin","Harry","Draco"];
let avatar={};

//////////////////////////////
// START
//////////////////////////////
function start(){

  user=document.getElementById("name").value||"Sorcier";

  document.getElementById("login").style.display="none";
  document.getElementById("app").style.display="block";

  document.getElementById("user").innerText=user;

  loadSave();
  generateSpells();
}

//////////////////////////////
// AVATAR SIMPLE
//////////////////////////////
function setAvatar(part,val){
  avatar[part]=val;
}

//////////////////////////////
// 100 SPELLS
//////////////////////////////
function generateSpells(){

  const e=["Feu","Glace","Éclair","Vent","Ténèbres"];
  const f=["Boule","Rayon","Nova","Strike","Burst"];

  spells=[];

  for(let i=0;i<100;i++){
    spells.push({
      name:e[i%5]+" "+f[i%5],
      element:e[i%5],
      power:Math.min(Math.floor(Math.random()*59)+1,59)
    });
  }

  renderSpells();
}

//////////////////////////////
// CREATE SPELL
//////////////////////////////
function createSpell(){

  let p=parseInt(document.getElementById("spellPower").value)||20;
  p=Math.min(p,59);

  spells.push({
    name:document.getElementById("spellName").value||"Sort",
    element:document.getElementById("spellElement").value,
    power:p
  });

  renderSpells();
}

//////////////////////////////
// GRIMOIRE
//////////////////////////////
function renderSpells(){

  const div=document.getElementById("spells");
  div.innerHTML="";

  spells.forEach((s,i)=>{

    const el=document.createElement("div");
    el.className="card";

    el.innerHTML=`🪄 ${s.name} ⚡${s.power}
    <button onclick="equip(${i})">Équiper</button>`;

    div.appendChild(el);
  });
}

function equip(i){
  if(equipped.length>=5) return alert("Max 5 sorts");
  equipped.push(spells[i]);
}

//////////////////////////////
// PLAYERS
//////////////////////////////
function loadPlayers(){

  const div=document.getElementById("players");
  div.innerHTML="";

  players.forEach(p=>{

    const el=document.createElement("div");
    el.className="card";

    el.innerHTML=`🧙 ${p}`;

    div.appendChild(el);
  });
}

//////////////////////////////
// COMBAT
//////////////////////////////
function attack(){

  if(!equipped.length) return alert("Aucun sort");

  const s=equipped[Math.random()*equipped.length|0];

  enemy-=Math.min(s.power,59);

  document.getElementById("enemy").innerText=enemy;
}

//////////////////////////////
// CHAT (LOCAL SAFE)
//////////////////////////////
const bad=["merde","fuck","shit","connard"];

function filter(t){
  bad.forEach(w=>{
    t=t.replace(new RegExp(w,"gi"),"#######");
  });
  return t;
}

function sendMsg(){

  const m=document.getElementById("msg").value;
  if(!m) return;

  const div=document.getElementById("chat");

  div.innerHTML+="<div class='card'><b>"+user+"</b>: "+filter(m)+"</div>";
}

//////////////////////////////
// SAVE
//////////////////////////////
function save(){

  const data={
    user,
    hp,
    spells,
    equipped,
    avatar
  };

  localStorage.setItem("magic_mmo",JSON.stringify(data));

  alert("💾 Sauvegardé !");
}

function loadSave(){

  const d=localStorage.getItem("magic_mmo");
  if(!d) return;

  const a=JSON.parse(d);

  user=a.user||user;
  hp=a.hp||100;
  spells=a.spells||[];
  equipped=a.equipped||[];
  avatar=a.avatar||{};
}

</script>

</body>
</html>
