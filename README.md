<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Anime Station</title>

<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap" rel="stylesheet">

<style>
*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:'Poppins',sans-serif;
}

body{
min-height:100vh;
overflow-x:hidden;
color:white;
}

/* BACKGROUND */
body::before{
content:'';
position:fixed;
inset:0;
background:url('https://www.image2url.com/r2/default/images/1778383894762-ecf9e5af-b21f-4bb1-978c-3fbc2349bf20.jpg') center/cover no-repeat;
z-index:-2;
filter:brightness(0.5) saturate(1.2);
}

body::after{
content:'';
position:fixed;
inset:0;
background:linear-gradient(135deg,rgba(26,0,46,0.75),rgba(50,0,75,0.65),rgba(86,0,125,0.65));
z-index:-1;
}

/* FRONT PAGE */
#frontPage{
position:fixed;
inset:0;
display:flex;
align-items:center;
justify-content:center;
background:rgba(0,0,0,0.6);
z-index:9999;
}

.frontBox{
text-align:center;
padding:40px;
border-radius:25px;
background:rgba(0,0,0,0.7);
}

.frontBox h1{
font-size:60px;
font-family:Impact;
background:linear-gradient(90deg,#ff66d9,#7afcff);
-webkit-background-clip:text;
color:transparent;
}

.frontBox button{
margin-top:20px;
padding:12px 40px;
border:none;
border-radius:50px;
font-weight:900;
background:linear-gradient(90deg,#ff66d9,#7a5cff);
color:white;
cursor:pointer;
}

/* TOPBAR */
.topbar{
position:fixed;
top:0;
left:0;
width:100%;
height:70px;
background:rgba(0,0,0,0.35);
display:flex;
align-items:center;
padding:0 20px;
z-index:1000;
}

.menu-btn{font-size:30px;cursor:pointer;margin-right:15px}
.logo{font-size:22px;font-weight:700}

/* SIDEBAR */
.sidebar{
position:fixed;
top:0;
left:-280px;
width:280px;
height:100%;
background:rgba(10,10,30,0.92);
padding-top:90px;
transition:0.4s;
z-index:2000;
}

.sidebar a{
display:block;
padding:16px;
margin:10px;
background:rgba(255,255,255,0.05);
color:white;
text-decoration:none;
border-radius:12px;
cursor:pointer;
}

/* MAIN */
.main{
padding:100px 20px 40px;
}

.hero{
background:rgba(255,255,255,0.08);
padding:25px;
border-radius:25px;
}

.card{
background:rgba(255,255,255,0.08);
padding:20px;
border-radius:18px;
margin-top:15px;
}

/* GAME */
canvas{
background:#000;
width:100%;
height:250px;
border-radius:15px;
}

.controls{
display:flex;
gap:10px;
margin-top:10px;
}

button, select, input{
flex:1;
padding:10px;
border:none;
border-radius:12px;
font-weight:700;
}

/* MUSIC BUTTON */
.musicBtn{
position:fixed;
bottom:20px;
right:20px;
padding:12px 16px;
border-radius:50px;
border:none;
background:#22c55e;
color:white;
font-weight:700;
}
</style>
</head>

<body>

<!-- FRONT PAGE -->
<div id="frontPage">
  <div class="frontBox">
    <h1>ANIME STATION</h1>
    <button onclick="enterApp()">START</button>
  </div>
</div>

<div class="sidebar" id="sidebar">
<a onclick="showPage('home')">Home</a>
<a onclick="showPage('game')">Game</a>
<a onclick="showPage('music')">Music</a>
<a onclick="showPage('anime')">Anime List</a>
<a onclick="showPage('settings')">Settings</a>
</div>

<div class="topbar">
<div class="menu-btn" onclick="openMenu()">☰</div>
<div class="logo">Anime Station</div>
</div>

<div class="main" id="content"></div>

<button class="musicBtn" onclick="toggleMusic()">Music</button>

<audio id="bgm" loop></audio>

<script>

function enterApp(){
document.getElementById("frontPage").style.display="none";
}

let state={
score:0,
volume:0.5
};

let c,ctx,player,enemy,run=false,loopId;

/* MUSIC LIST */
const musicList=[
{name:"Anime Lo-fi",url:"https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3"},
{name:"Epic Anime",url:"https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3"},
{name:"Chill Beats",url:"https://www.soundhelix.com/examples/mp3/SoundHelix-Song-5.mp3"}
];

/* MENU */
function openMenu(){
document.getElementById('sidebar').style.left='0';
}

function closeMenu(){
document.getElementById('sidebar').style.left='-280px';
}

/* MUSIC FIXED (OLD STYLE) */
function toggleMusic(){
let a=document.getElementById("bgm");
if(!a.src) a.src=musicList[0].url;
a.paused?a.play():a.pause();
}

function loadMusic(url){
let a=document.getElementById("bgm");
a.src=url;
a.volume=state.volume;
a.play();
}

/* PAGE SYSTEM */
function showPage(p){

if(p==="home"){
document.getElementById("content").innerHTML=`
<div class="hero">
<h1>Home</h1>
<div class="card">Score: ${state.score}</div>
</div>`;
}

if(p==="game"){
document.getElementById("content").innerHTML=`
<div class="hero">
<h1>Game</h1>

<canvas id="game"></canvas>

<div class="controls">
<button onclick="move(-25)">Left</button>
<button onclick="startGame()">Start</button>
<button onclick="move(25)">Right</button>
</div>

<div class="controls">
<button onclick="stopGame()">OFF</button>
<button onclick="resetGame()">RESET</button>
</div>

<div class="card">Score: <span id="score">${state.score}</span></div>
</div>`;
initGame();
}

if(p==="music"){
document.getElementById("content").innerHTML=`
<div class="hero">
<h1>Music</h1>

<div class="card">
<select id="musicSelect">
${musicList.map((m,i)=>`<option value="${i}">${m.name}</option>`).join("")}
</select>

<button onclick="playSelected()">Play</button>
<button onclick="stopMusic()">Stop</button>
</div>

<div class="card">
Now Playing: <span id="now">None</span>
</div>
</div>`;
}

if(p==="anime"){
document.getElementById("content").innerHTML=`
<div class="hero">
<h1>Anime List</h1>
<div class="card">Naruto</div>
<div class="card">One Piece</div>
<div class="card">Attack on Titan</div>
<div class="card">Jujutsu Kaisen</div>
<div class="card">Demon Slayer</div>
</div>`;
}

if(p==="settings"){
document.getElementById("content").innerHTML=`
<div class="hero">
<h1>Settings</h1>

<div class="card">
Volume
<input type="range" min="0" max="1" step="0.1"
value="${state.volume}"
oninput="setVolume(this.value)">
</div>
</div>`;
}

closeMenu();
}

/* MUSIC CONTROL */
function playSelected(){
let i=document.getElementById("musicSelect").value;
document.getElementById("now").innerText=musicList[i].name;
loadMusic(musicList[i].url);
}

function stopMusic(){
let a=document.getElementById("bgm");
a.pause();
a.currentTime=0;
}

function setVolume(v){
state.volume=v;
document.getElementById("bgm").volume=v;
}

/* GAME */
function initGame(){
c=document.getElementById("game");
ctx=c.getContext("2d");
c.width=400;c.height=250;

player={x:180,y:210,w:40,h:20};
enemy={x:100,y:0,w:40,h:20};

run=false;
draw();
}

function draw(){
if(!run) return;

ctx.clearRect(0,0,400,250);

ctx.fillStyle="cyan";
ctx.fillRect(player.x,player.y,player.w,player.h);

ctx.fillStyle="red";
ctx.fillRect(enemy.x,enemy.y,enemy.w,enemy.h);

enemy.y+=4;

if(enemy.y>250){
enemy.y=0;
enemy.x=Math.random()*360;
state.score++;
document.getElementById("score").innerText=state.score;
}

if(
enemy.x<player.x+player.w &&
enemy.x+enemy.w>player.x &&
enemy.y<player.y+player.h &&
enemy.y+enemy.h>player.y
){
resetGame();
return;
}

loopId=requestAnimationFrame(draw);
}

function startGame(){
run=true;
draw();
}

function stopGame(){
run=false;
cancelAnimationFrame(loopId);
}

function resetGame(){
run=false;
cancelAnimationFrame(loopId);
state.score=0;
document.getElementById("score").innerText=0;
enemy.y=0;
enemy.x=100;
}

function move(v){
player.x+=v;
if(player.x<0)player.x=0;
if(player.x>360)player.x=360;
}

showPage('home');

</script>

</body>
</html>