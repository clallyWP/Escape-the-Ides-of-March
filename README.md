# Escape-the-Ides-of-March
Interactive game to review for New York State English Regents based on June 2017 exam
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Caesar: Escape the Ides</title>

<style>
body {
  text-align:center;
  font-family:"Georgia", serif;
  background:#f2e6c9;
}

h2 { color:#5a2d0c; }

canvas {
  background:#e2c48d;
  display:block;
  margin:auto;
  border:4px solid #5a2d0c;
}

#status {
  font-size:20px;
  font-weight:bold;
}

#counter {
  font-size:16px;
}
</style>
</head>

<body>

<h2>🏛️ Julius Caesar: Escape the Ides of March</h2>
<p>Walk Rome. Answer wisely. Survive the Senate.</p>

<canvas id="game" width="800" height="500"></canvas>
<p id="status"></p>
<p id="counter"></p>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

// PLAYER
let caesar = {x:50,y:250};
let awareness = 0;
let currentQ = 0;

// ANSWER KEY ✅
const answers = [1,2,4,3,1,4,3,1,1,3,1,3,4,2,3,4,1,3,2,4,1,2,2,3];

// FULL QUESTIONS ✅
const questions = [
"1) First paragraph purpose?\n1 reason 2 mystery 3 aunt prefs 4 flaws",
"2) Uncle implied to be?\n1 uncomfortable 2 careless 3 angry 4 disappointed",
"3) Aunt youthful trait?\n1 courageous 2 compassionate 3 resourceful 4 intelligent",
"4) Line 27 shows?\n1 discomfort 2 happiness 3 isolation 4 affection",
"5) Why affection?\n1 taught Shakespeare 2 warning 3 emotion 4 unknown",
"6) Theme?\n1 generosity 2 resentment 3 discipline 4 music loss",
"7) 'understood why'?\n1 unaware 2 avoids 3 sacrifice 4 recognition",
"8) Lines meaning?\n1 duties 2 culture desire 3 fear 4 memory",
"9) Ending shows?\n1 bleak 2 unusual 3 skills 4 hostile",
"10) Realization?\n1 meeting 2 plan 3 wasted life 4 music",
"11) Highway 99?\n1 culture 2 economy 3 childhood 4 region",
"12) POV influenced by?\n1 farm 2 nostalgia 3 labor 4 expectations",
"13) History wants?\n1 repeat 2 forgotten 3 comforted 4 heard",
"14) Figurative meaning?\n1 regret 2 challenge 3 success 4 wrong choice",
"15) Tone?\n1 caution 2 accusation 3 excitement 4 relief",
"16) Details show?\n1 music/plant 2 plant diff 3 forensic 4 research impact",
"17) 'But' shows?\n1 debate 2 compare 3 similarity 4 learning",
"18) Lines connect?\n1 species 2 studies 3 human 4 predator",
"19) Quotes show?\n1 deception 2 debate 3 confusion 4 resentment",
"20) Plants suggest?\n1 resist 2 avoid 3 sound 4 intent",
"21) Evidence shows?\n1 symbiosis 2 attack 3 waste 4 mutate",
"22) Signalling?\n1 reproduce 2 defend 3 human react 4 climate",
"23) Final idea?\n1 human superior 2 adaptation 3 need awareness 4 ignored",
"24) Credibility?\n1 order 2 compare 3 evidence 4 anecdote"
];

// LOCATIONS
const locations = [
  {x:700,y:100,name:"SENATE"},
  {x:400,y:200,name:"FORUM"},
  {x:650,y:400,name:"TEMPLE"},
  {x:150,y:100,name:"COLOSSEUM"}
];

let keys = {};
document.addEventListener("keydown",e=>keys[e.key]=true);
document.addEventListener("keyup",e=>keys[e.key]=false);

// BOSS SYSTEM
let bossActive=false;
let boss={hp:20};

function startBoss(){
  bossActive=true;
  boss.hp=20-Math.floor(awareness/2);
  if(boss.hp<5) boss.hp=5;

  alert("⚠️ The Senate surrounds you...\nBeware the Ides of March!");
}

function bossTurn(){
  if(Math.random()<0.6){
    awareness--;
    alert("🗡️ The Senate strikes!");
  }
}

function playerAttack(){
  let dmg=Math.floor(Math.random()*3)+1;
  if(awareness>=16) dmg+=2;
  boss.hp-=dmg;
  alert("⚔️ You strike for "+dmg);
}

// DRAW
function draw(){
  ctx.clearRect(0,0,800,500);

  // Roads
  ctx.strokeStyle="#7a5a2c";
  ctx.beginPath();
  ctx.moveTo(0,250); ctx.lineTo(800,250);
  ctx.moveTo(400,0); ctx.lineTo(400,500);
  ctx.stroke();

  // Caesar
  ctx.fillStyle="purple";
  ctx.fillRect(caesar.x, caesar.y, 20, 20);

  // Landmarks
  locations.forEach(l=>{
    ctx.fillStyle="maroon";
    ctx.fillRect(l.x,l.y,30,30);
    ctx.fillStyle="black";
    ctx.fillText(l.name,l.x-20,l.y-5);
  });

  // UI
  ctx.fillStyle="black";
  ctx.fillText("Awareness: "+awareness,10,20);

  document.getElementById("counter").innerText =
    "Ides approaching... Questions left: "+(24-currentQ);
}

// MOVE
function move(){
  if(keys["ArrowUp"]) caesar.y-=3;
  if(keys["ArrowDown"]) caesar.y+=3;
  if(keys["ArrowLeft"]) caesar.x-=3;
  if(keys["ArrowRight"]) caesar.x+=3;
}

// COLLISION
function collide(a,b){
  return a.x<b.x+30 && a.x+20>b.x &&
         a.y<b.y+30 && a.y+20>b.y;
}

// QUESTIONS
function askQuestion(){
  if(currentQ>=24) return endGame();

  let ans = prompt(questions[currentQ]);

  if(Number(ans)===answers[currentQ]){
    awareness++;
    alert("✅ Correct! You sense danger...");
  } else {
    alert("❌ Incorrect...");
  }

  currentQ++;
}

// ENDGAME WITH BOSS
function endGame(){

  if(!bossActive){
    startBoss();
    return;
  }

  if(boss.hp>0 && awareness>0){

    let choice = prompt(
      "🏛️ FINAL BATTLE\n" +
      "Awareness: "+awareness+"\n" +
      "Senate HP: "+boss.hp+"\n\n" +
      "1 Attack\n2 Defend"
    );

    if(choice=="1") playerAttack();
    else awareness++;

    bossTurn();
    setTimeout(endGame,350);
    return;
  }

  // FINAL RESULT
  if(boss.hp<=0){
    document.getElementById("status").innerText =
      "🟢 VICTORY: You defeated the Senate!";
    document.body.style.background="#c8f7c5";
  } else {
    document.getElementById("status").innerText =
      "🔴 ET TU, BRUTE? You were assassinated.";
    document.body.style.background="#7a1c1c";
  }
}

// EVENTS
function checkEvents(){
  locations.forEach(l=>{
    if(collide(caesar,l)) askQuestion();
  });
}

// LOOP
function loop(){
  move();
  draw();
  checkEvents();
  requestAnimationFrame(loop);
}

loop();
</script>

</body>
</html>
``
