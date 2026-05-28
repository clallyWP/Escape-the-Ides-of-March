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
  font-family:Georgia;
  background:#f4e6c1;
}
canvas {
  display:block;
  margin:auto;
  border:5px solid #5a2c0c;
  background:#d8c08a;
}
</style>
</head>

<body>

<h2>🏛️ Caesar: Escape the Ides (Zelda Edition)</h2>
<p>Move with arrows. Press SPACE to interact.</p>

<canvas id="game" width="800" height="500"></canvas>
<p id="status"></p>
<p id="counter"></p>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

// PLAYER
let caesar = {x:50, y:50, size:20};
let awareness = 0;
let currentQ = 0;

// CONTROL
let keys = {};
document.addEventListener("keydown",e=>keys[e.key]=true);
document.addEventListener("keyup",e=>keys[e.key]=false);

// ANSWERS
const answers = [1,2,4,3,1,4,3,1,1,3,1,3,4,2,3,4,1,3,2,4];

// QUESTIONS
const questions = [

"1. A primary function of the first paragraph is to (1) establish the reason for the meeting (2) create an atmosphere of mystery (3) identify preferences of the narrator’s aunt (4) reveal flaws in the narrator’s character",

"2. In lines 1 through 9, the commentary about the letter implies that the narrator believes his uncle is (1) uncomfortable with changes (2) careless about details (3) angry with his wife (4) disappointed at his decision",

"3. The details in lines 13 through 20 suggest that in her youth Aunt Georgiana was (1) courageous yet hesitant (2) compassionate yet critical (3) resourceful yet cautious (4) intelligent yet impulsive",

"4. Line 27, “For thirty years my aunt had not been farther than fifty miles from the homestead” reinforces a sense of (1) discomfort (2) happiness (3) isolation (4) affection",

"5. Which statement best explains the narrator’s affection? (1) It was to her… I read my first Shakespeare (2) “Don’t love it so well…” (3) Her lip quivered… (4) I never knew how far it bore her",

"6. Lines 36 through 38 develop a central theme by (1) recalling generosity (2) suggesting resentment (3) emphasizing discipline (4) implying Aunt Georgiana missed music",

"7. When the narrator states that he “understood why,” he is implying that his Aunt Georgiana (1) knew little about trends (2) avoided discussing skills (3) realized what she has given up (4) needed recognition",

"8. Lines 47 through 52 contribute to a central idea by depicting Aunt Georgiana’s (1) concern for daily responsibilities (2) desire for cultural experiences (3) fear of separation (4) fixation on painful memories",

"9. The author’s choice of how to end the story places emphasis on Aunt Georgiana’s (1) bleak future (2) unusual lifestyle (3) domestic skills (4) hostile attitude",

"10. Which quotation best reflects the narrator’s realization? (1) meet her at station (2) take my aunt to concert (3) overwhelming sense of waste (4) sound poured on",

"11. The poet’s purpose in referencing “Highway 99” is to establish (1) cultural connection (2) criticism of economy (3) difficult childhood (4) regional landscape",

"12. The narrator’s point of view is influenced by (1) farm work experience (2) nostalgia (3) relatives’ labor (4) expectations",

"13. The personification stresses history’s desire to be (1) repeated (2) forgotten (3) comforted (4) heard",

"14. The figurative language implies the narrator (1) regrets leaving (2) anticipates challenges (3) expects success (4) made a wrong decision",

"15. The first paragraph conveys a sense of (1) caution (2) accusation (3) excitement (4) relief",

"16. Details mainly establish the (1) relationship of plant science and music (2) difference between houseplants and wild plants (3) importance of forensic science (4) impact of early studies",

"17. The author uses “But” to (1) express controversy (2) compare texts (3) express similarity (4) compare study abilities",

"18. Lines 37 through 41 indicate a connection (1) among plant species (2) among studies (3) between humans and plants (4) between predators and prey",

"19. Quotation marks acknowledge (1) deception (2) debate (3) confusion (4) resentment",

"20. Lines 58 through 62 suggest plants (1) resist cooperation (2) avoid change (3) produce sound (4) possess intent",

"21. Evidence demonstrates that plants may (1) develop relationships (2) attack weak organisms (3) waste resources (4) produce mutations",

"22. “Plant signalling” refers to how plants (1) reproduce (2) protect themselves (3) react to humans (4) adapt to climate",

"23. The final paragraph suggests that (1) humans are superior (2) species develop based on needs (3) plants need self-awareness (4) scientists dismissed findings",

"24. The text’s credibility relies on (1) order of importance (2) extended comparison (3) observable evidence (4) personal anecdotes"

];
``

// 🏛️ TILE MAP (Zelda style)
const tileSize = 40;

function drawMap(){
  for(let x=0;x<800;x+=tileSize){
	for(let y=0;y<500;y+=tileSize){

  	ctx.fillStyle="#d8c08a";
  	ctx.fillRect(x,y,tileSize,tileSize);

  	ctx.strokeStyle="#c2a36d";
  	ctx.strokeRect(x,y,tileSize,tileSize);
	}
  }

  // Roads
  ctx.fillStyle="#a88c5f";
  ctx.fillRect(0,240,800,40);
  ctx.fillRect(380,0,40,500);
}

// LOCATIONS (ZONES)
const locations = [
const locations = [
  {x:700,y:50,name:"Senate",visited:false,questionsDone:0},
  {x:400,y:100,name:"Forum",visited:false,questionsDone:0},
  {x:650,y:400,name:"Temple",visited:false,questionsDone:0},
  {x:100,y:350,name:"Colosseum",visited:false,questionsDone:0}
];

// DRAW
function draw(){
  ctx.clearRect(0,0,800,500);

  drawMap();

  // Caesar
  ctx.fillStyle="purple";
  ctx.fillRect(caesar.x, caesar.y, caesar.size, caesar.size);

  // Locations
  locations.forEach(l=>{
	ctx.fillStyle = l.visited ? "gray" : "darkred";
	ctx.fillRect(l.x,l.y,30,30);

	ctx.fillStyle="black";
	ctx.fillText(l.name,l.x-10,l.y-5);
  });

  // UI
  ctx.fillStyle="black";
  ctx.fillText("Awareness: "+awareness,10,20);

  document.getElementById("counter").innerText =
	"Questions left:” + (24-currentQ) 
}

// MOVE (smooth Zelda-style)
function move(){
  if(keys["ArrowUp"]) caesar.y-=2;
  if(keys["ArrowDown"]) caesar.y+=2;
  if(keys["ArrowLeft"]) caesar.x-=2;
  if(keys["ArrowRight"]) caesar.x+=2;
}

// INTERACTION
function checkInteraction(){
  if(!keys[" "]) return;

  locations.forEach(l=>{
	if(distance(caesar,l)<40 && !l.visited){
 
function triggerQuestion(location){

  // ✅ If game is complete → go to boss
  if(currentQ >= 24){
	endGame();
	return;
  }

  // ✅ Only allow 6 questions per location
  if(location.questionsDone >= 6){
	alert(location.name + " is complete. Move to another location!");
	return;
  }

  let ans = prompt(questions[currentQ]);

  if(Number(ans) === answers[currentQ]){
	awareness++;
	alert("✅ Correct! The plot becomes clearer...");
  } else {
	alert("❌ Incorrect...");
  }

  currentQ++;
  location.questionsDone++;

  // ✅ Mark location complete
  if(location.questionsDone >= 6){
	location.visited = true;
	alert("🏛️ You have completed " + location.name + "!");
  }

}

// DISTANCE CHECK
function distance(a,b){
  return Math.hypot(a.x - b.x, a.y - b.y);
}

// QUESTIONS
function triggerQuestion(location){

  if(currentQ>=questions.length){
	endGame();
	return;
  }

  let ans = prompt(questions[currentQ]);

  if(Number(ans)===answers[currentQ]){
	awareness++;
	alert("✅ Correct!");
  } else {
	alert("❌ Incorrect!");
  }

  location.visited = true;
  currentQ++;
}

// 🏛️ FINAL BOSS
let bossActive=false;
let bossHP=10;

function startBoss(){
  bossActive=true;
  bossHP = 10 - Math.floor(awareness/2);
  if(bossHP<3) bossHP=3;

  alert("⚠️ The Senate confronts you!");
}

function bossFight(){

  if(!bossActive){ startBoss(); return; }

  if(bossHP>0 && awareness>0){
	let c = prompt(
  	"Senate HP: "+bossHP+"\n"+
  	"Awareness: "+awareness+"\n"+
  	"1 Attack\n2 Defend"
	);

	if(c=="1"){
  	let dmg = Math.floor(Math.random()*3)+1;
  	bossHP -= dmg;
  	alert("You strike for "+dmg);
	} else {
  	awareness++;
	}

	if(Math.random()<0.6){
  	awareness--;
  	alert("The Senate attacks!");
	}

	setTimeout(bossFight,300);
	return;
  }

  if(bossHP<=0){
	document.getElementById("status").innerText =
  	"🟢 You survived Rome!";
  } else {
	document.getElementById("status").innerText =
  	"🔴 You were assassinated.";
  }
}

// END GAME
function endGame(){
  bossFight();
}

// LOOP
function loop(){
  move();
  function checkInteraction(){
  if(!keys[" "]) return;

  locations.forEach(l=>{
	if(distance(caesar,l) < 40){
  	triggerQuestion(l);
	}
  });
}
);
  draw();
  requestAnimationFrame(loop);
}

loop();
</script>

</body>
</html>
``

