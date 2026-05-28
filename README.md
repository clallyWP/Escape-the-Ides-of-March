# Escape-the-Ides-of-March
Interactive game to review for New York State English Regents based on June 2017 exam
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Caesar: Escape the Ides</title>
<style>
  body { text-align:center; font-family:Arial; }
  canvas { background:#e8d8b5; display:block; margin:auto; }
</style>
</head>
<body>

<h2>Julius Caesar: Escape the Ides of March</h2>
<p>Answer questions across Rome. 16+ correct = survive.</p>

<canvas id="game" width="800" height="500"></canvas>
<p id="status"></p>
<p id="counter"></p>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

const correctSound = new Audio("https://actions.google.com/sounds/v1/cartoon/clang_and_wobble.ogg");
const wrongSound = new Audio("https://actions.google.com/sounds/v1/cartoon/wood_plank_flicks.ogg");

let caesar = {x:50, y:250, size:20};
let awareness = 0;
let currentQ = 0;

// ✅ OFFICIAL ANSWERS [2](https://scric-my.sharepoint.com/personal/clally_wpcsd_org/Documents/Microsoft%20Copilot%20Chat%20Files/June2017MCScoring.pdf)
const answers = [1,2,4,3,1,4,3,1,1,3,1,3,4,2,3,4,1,3,2,4,1,2,2,3];

// ✅ FULL QUESTIONS [1](https://scric-my.sharepoint.com/personal/clally_wpcsd_org/Documents/Microsoft%20Copilot%20Chat%20Files/June2017MC.pdf)
const questions = [
"1. A primary function of the first paragraph is to (1) establish the reason for the meeting (2) create an atmosphere of mystery (3) identify preferences of the narrator’s aunt (4) reveal flaws in the narrator’s character",

"2. In lines 1 through 9, the commentary about the letter implies that the narrator believes his uncle is (1) uncomfortable with changes (2) careless about details (3) angry with his wife (4) disappointed at his decision",

"3. The details in lines 13 through 20 suggest that in her youth Aunt Georgiana was (1) courageous yet hesitant (2) compassionate yet critical (3) resourceful yet cautious (4) intelligent yet impulsive",

"4. Line 27 reinforces a sense of (1) discomfort (2) happiness (3) isolation (4) affection",

"5. Which explains narrator’s affection? (1) read first Shakespeare (2) warning quote (3) lip quivered (4) unknown journey",

"6. Lines 36–38 develop theme by (1) generosity (2) resentment (3) discipline (4) missed music",

"7. 'understood why' implies aunt (1) unaware trends (2) avoids discussion (3) realizes sacrifice (4) needs recognition",

"8. Lines 47–52 show (1) duties (2) desire for culture (3) fear separation (4) fixation on pain",

"9. Ending emphasizes (1) bleak future (2) unusual lifestyle (3) domestic skills (4) hostile attitude",

"10. Realization quote (1) meet aunt (2) concert plan (3) waste of life (4) music current",

"11. Purpose of Highway 99 reference (1) cultural connection (2) criticize economy (3) childhood struggle (4) regional landscape",

"12. Perspective influenced by (1) farm work (2) nostalgia (3) relatives’ labor (4) expectations",

"13. Personification suggests history wants to be (1) repeated (2) forgotten (3) comforted (4) heard",

"14. Figurative language implies narrator (1) regrets leaving (2) expects challenges (3) expects success (4) made wrong choice",

"15. First paragraph conveys (1) caution (2) accusation (3) excitement (4) relief",

"16. Details establish (1) music + plants (2) house vs wild (3) forensic science (4) impact of early research",

"17. 'But' shows (1) controversy (2) compares books (3) similarities (4) plant learning",

"18. Lines 37–41 indicate (1) species connection (2) studies connection (3) humans + plants (4) predator relationship",

"19. Quotation marks show (1) deception (2) debate (3) confusion (4) resentment",

"20. Lines 58–62 suggest plants (1) resist cooperation (2) avoid change (3) produce sound (4) have intent",

"21. Evidence shows plants (1) develop symbiosis (2) attack weak (3) waste resources (4) mutate",

"22. 'Plant signalling' refers to (1) reproduction (2) defense (3) human response (4) climate adaptation",

"23. Final paragraph suggests (1) humans superior (2) species adapt to needs (3) plants need awareness (4) science ignored findings",

"24. Credibility based on (1) order (2) comparison (3) evidence (4) anecdotes"
];

// MAP
const locations = [
  {x:700,y:100,name:"Senate"},
  {x:400,y:200,name:"Forum"},
  {x:650,y:400,name:"Temple"},
  {x:150,y:100,name:"Colosseum"}
];

let npc = {x:350,y:400};
let keys = {};

document.addEventListener("keydown", e=>keys[e.key]=true);
document.addEventListener("keyup", e=>keys[e.key]=false);

function move(){
  if(keys["ArrowUp"]) caesar.y-=3;
  if(keys["ArrowDown"]) caesar.y+=3;
  if(keys["ArrowLeft"]) caesar.x-=3;
  if(keys["ArrowRight"]) caesar.x+=3;
}

function draw(){
  ctx.clearRect(0,0,800,500);

  // roads
  ctx.strokeStyle="gray";
  ctx.beginPath();
  ctx.moveTo(0,250); ctx.lineTo(800,250);
  ctx.moveTo(400,0); ctx.lineTo(400,500);
  ctx.stroke();

  // Caesar
  ctx.fillStyle="purple";
  ctx.fillRect(caesar.x, caesar.y, 20, 20);

  // Locations
  locations.forEach(l=>{
	ctx.fillStyle="red";
	ctx.fillRect(l.x,l.y,30,30);
	ctx.fillText(l.name,l.x-10,l.y-5);
  });

  // NPC
  ctx.fillStyle="blue";
  ctx.fillRect(npc.x,npc.y,20,20);
  ctx.fillText("Senator",npc.x-15,npc.y-5);

  ctx.fillStyle="black";
  ctx.fillText("Awareness: "+awareness,10,20);

  // ✅ NEW COUNTDOWN (QUESTIONS LEFT)
  let remaining = 24 - currentQ;
  document.getElementById("counter").innerText =
	"Ides approaching... Questions remaining: " + remaining;
}

// COLLISION
function collide(a,b){
  return a.x<b.x+30 && a.x+20>b.x && a.y<b.y+30 && a.y+20>b.y;
}

// QUESTIONS
function askQuestion(){
  if(currentQ>=24) return endGame();

  let user = prompt(questions[currentQ]+"\nEnter 1-4:");

  if(Number(user)===answers[currentQ]){
	awareness++;
	correctSound.play();
	alert("✅ Correct! You sense danger...");
  } else {
	wrongSound.play();
	alert("❌ Wrong... conspirators remain hidden.");
  }

  currentQ++;
}

// NPC
function talkNPC(){
  let hint="";
  if(awareness<5) hint="Trust no one, Caesar...";
  else if(awareness<10) hint="Senators whisper behind pillars...";
  else if(awareness<16) hint="Beware the Ides of March!";
  else hint="You see all... you may survive.";

  alert(hint);
}

// END GAME
function endGame(){

  // ✅ Start boss fight when questions end
  if(!bossActive){
	startBossFight();
	return;
  }

  // ✅ Boss fight loop
  if(boss.hp > 0 && awareness > 0){

	let choice = prompt(
  	"🏛️ FINAL BATTLE: THE SENATE\n\n" +
  	"Your Awareness: " + awareness + "\n" +
  	"Senate HP: " + boss.hp + "\n\n" +
  	"Choose:\n1 ⚔️ Attack\n2 🛡️ Defend"
	);

	if(choice == "1"){
  	playerAttack();
	} else {
  	alert("🛡️ You brace yourself and regain composure.");
  	awareness++; // small recovery
	}

	bossTurn();

	// Continue battle
	setTimeout(endGame, 400);
	return;
  }

  // ✅ FINAL OUTCOME
  if(boss.hp <= 0){
	document.getElementById("status").innerText =
  	"🟢 YOU SURVIVED!\nYou outmaneuvered the Senate and secured your rule!";
	document.body.style.background = "lightgreen";

  } else {
	document.getElementById("status").innerText =
  	"🔴 ET TU, BRUTE?\nThe Senate has assassinated you.";
	document.body.style.background = "darkred";
  }

  bossActive = false;
}
