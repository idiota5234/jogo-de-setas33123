```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Rhythm Game</title>

<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap" rel="stylesheet">

<style>

body{
background:#0a0a0a;
color:white;
font-family:'Orbitron',sans-serif;
text-align:center;
margin:0;
}

h1{
font-size:42px;
text-shadow:0 0 10px cyan;
}

button{
padding:12px 25px;
font-size:18px;
margin:10px;
border:none;
border-radius:8px;
cursor:pointer;
background:#00e5ff;
color:black;
}

#game{
display:none;
position:relative;
width:400px;
height:500px;
margin:auto;
background:#111;
overflow:hidden;
border:3px solid white;
margin-top:20px;
}

.note{
position:absolute;
width:80px;
height:40px;
display:flex;
justify-content:center;
align-items:center;
font-size:34px;
background:#222;
border-radius:8px;
}

.target{
position:absolute;
bottom:60px;
width:80px;
height:40px;
display:flex;
justify-content:center;
align-items:center;
font-size:34px;
background:#0f0;
color:black;
border-radius:8px;
}

#healthBar{
width:400px;
height:20px;
background:red;
margin:auto;
}

#health{
height:100%;
width:100%;
background:lime;
}

#timer{
font-size:22px;
color:cyan;
margin-top:10px;
}

#rating{
position:absolute;
top:200px;
left:50%;
transform:translateX(-50%);
font-size:45px;
display:none;
}

.star{
position:absolute;
font-size:20px;
color:yellow;
animation:star 0.6s linear;
}

@keyframes star{
0%{transform:scale(0.5);opacity:1;}
100%{transform:scale(2);opacity:0;}
}

#gameOver,#victory{
display:none;
position:fixed;
top:0;
left:0;
width:100%;
height:100%;
background:black;
color:white;
justify-content:center;
align-items:center;
flex-direction:column;
font-size:40px;
}

</style>
</head>

<body>

<h1>Rhythm Game</h1>

<div id="menu">

<h2>Escolha a dificuldade</h2>

<button onclick="startGame('easy')">Fácil</button>
<button onclick="startGame('normal')">Normal</button>
<button onclick="startGame('hard')">Difícil</button>

<br><br>

<button onclick="startGame('normal')">🎮 Jogar Agora</button>

<button onclick="baixarJogo()">💾 Salvar Jogo</button>

<h3>Cores das setas</h3>

⬅ <input type="color" id="colorLeft" value="#ffffff">
⬆ <input type="color" id="colorUp" value="#ffffff">
⬇ <input type="color" id="colorDown" value="#ffffff">
➡ <input type="color" id="colorRight" value="#ffffff">

</div>

<div id="timer">Tempo: 03:00</div>

<div id="healthBar">
<div id="health"></div>
</div>

<div id="game">

<div class="target" style="left:0">⬅</div>
<div class="target" style="left:100px">⬆</div>
<div class="target" style="left:200px">⬇</div>
<div class="target" style="left:300px">➡</div>

<div id="rating"></div>

</div>

<div id="gameOver">

💀 GAME OVER

<br><br>

<button onclick="location.reload()">Reiniciar</button>

</div>

<div id="victory">

🏆 VITÓRIA

<br><br>

<button onclick="location.reload()">Jogar novamente</button>

</div>

<audio id="music">
<source src="https://cdn.pixabay.com/audio/2022/03/15/audio_c8a3a1bfe0.mp3">
</audio>

<script>

let game=document.getElementById("game")
let menu=document.getElementById("menu")
let music=document.getElementById("music")

let notes=[]
let speed=4
let spawnRate=900

let health=100
let healthBar=document.getElementById("health")

let gameRunning=false

let timerDisplay=document.getElementById("timer")

let time=180

let rating=document.getElementById("rating")

let arrows=["⬅","⬆","⬇","➡"]

function startGame(diff){

menu.style.display="none"
game.style.display="block"

if(diff=="easy"){
speed=3
spawnRate=1200
}

if(diff=="normal"){
speed=4
spawnRate=900
}

if(diff=="hard"){
speed=6
spawnRate=600
}

music.play()

gameRunning=true

setInterval(spawnNote,spawnRate)

startTimer()

}

function spawnNote(){

if(!gameRunning)return

let lane=Math.floor(Math.random()*4)

let note=document.createElement("div")

note.classList.add("note")

note.innerHTML=arrows[lane]

note.style.left=(lane*100)+"px"
note.style.top="0px"

let colors=[
document.getElementById("colorLeft").value,
document.getElementById("colorUp").value,
document.getElementById("colorDown").value,
document.getElementById("colorRight").value
]

note.style.color=colors[lane]

game.appendChild(note)

notes.push({element:note,lane:lane,y:0})

}

function update(){

if(gameRunning){

notes.forEach((n,i)=>{

n.y+=speed

n.element.style.top=n.y+"px"

if(n.y>450){

damage(10)

n.element.remove()

notes.splice(i,1)

}

})

}

requestAnimationFrame(update)

}

update()

function showRating(text,color){

rating.innerText=text
rating.style.color=color
rating.style.display="block"

setTimeout(()=>{

rating.style.display="none"

},300)

}

function createStars(){

for(let i=0;i<6;i++){

let star=document.createElement("div")

star.classList.add("star")

star.innerHTML="⭐"

star.style.left=(180+Math.random()*40)+"px"
star.style.top=(350+Math.random()*40)+"px"

game.appendChild(star)

setTimeout(()=>{star.remove()},600)

}

}

function damage(amount){

health-=amount

if(health<0)health=0

healthBar.style.width=health+"%"

if(health<=0){

gameRunning=false

music.pause()

document.getElementById("gameOver").style.display="flex"

}

}

function startTimer(){

let interval=setInterval(()=>{

if(!gameRunning)return

time--

let m=Math.floor(time/60)
let s=time%60

if(s<10)s="0"+s

timerDisplay.innerText="Tempo: "+m+":"+s

if(time<=0){

clearInterval(interval)

gameRunning=false

music.pause()

document.getElementById("victory").style.display="flex"

}

},1000)

}

document.addEventListener("keydown",(e)=>{

if(!gameRunning)return

let lane=null

if(e.key==="ArrowLeft") lane=0
if(e.key==="ArrowUp") lane=1
if(e.key==="ArrowDown") lane=2
if(e.key==="ArrowRight") lane=3

let hit=false

notes.forEach((n,i)=>{

let diff=Math.abs(n.y-390)

if(n.lane===lane && diff<60){

hit=true

if(diff<10){

showRating("SICK","cyan")
createStars()

}

else if(diff<30){

showRating("GOOD","lime")

}

else{

showRating("BAD","orange")

}

n.element.remove()

notes.splice(i,1)

}

})

if(!hit){

damage(10)

showRating("MISS","red")

}

})

function baixarJogo(){

let conteudo=document.documentElement.outerHTML

let blob=new Blob([conteudo],{type:"text/html"})

let link=document.createElement("a")

link.href=URL.createObjectURL(blob)

link.download="jogo_ritmo.html"

link.click()

}

</script>

</body>
</html>
```

