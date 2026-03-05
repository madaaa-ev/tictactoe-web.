<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>TicTacToe Adaptive Difficulty</title>

<style>

body{
margin:0;
background:url('https://raw.githubusercontent.com/shiro-nesu/database-img/main/file_1772714592732.jpeg') no-repeat center center fixed;
background-size:cover;
color:white;
font-family:'Segoe UI',sans-serif;

display:flex;
justify-content:center;
align-items:center;
min-height:100vh;
}

/* START SCREEN */

#startScreen{
display:flex;
flex-direction:column;
align-items:center;
justify-content:center;
text-align:center;

background:rgba(0,0,0,0.55);
padding:30px 25px;
border-radius:14px;

max-width:500px;
gap:10px;
}

/* TITLE */

h1{
font-weight:900;
letter-spacing:3px;
text-transform:uppercase;
font-size:48px;
margin:0;
}

/* SUBTEXT */

#startScreen p{
margin:0;
font-size:18px;
opacity:.75;
letter-spacing:2px;
}

/* DESCRIPTION */

.desc{
max-width:420px;
font-size:15px;
line-height:1.5;
opacity:.9;
}

/* BUTTON */

button{
padding:14px 34px;
border:none;
border-radius:12px;
background:#222;
color:white;
font-weight:700;
font-size:16px;
letter-spacing:1px;
cursor:pointer;
transition:.3s;
}

button:hover{
background:#444;
transform:scale(1.05);
}

/* BOARD */

#board{
display:grid;
grid-template-columns:repeat(3,100px);
gap:10px;
margin-top:20px;
}

.cell{
width:100px;
height:100px;
background:linear-gradient(145deg,#1b1b1b,#252525);
display:flex;
justify-content:center;
align-items:center;
font-size:38px;
font-weight:900;
border-radius:14px;
cursor:pointer;
transition:.2s;
text-shadow:0 0 10px rgba(255,255,255,0.3);
box-shadow:inset 0 0 10px rgba(0,0,0,0.6);
}

.cell:hover{
background:#333;
transform:scale(1.05);
}

.cell.X{color:#ff4d4d;}
.cell.O{color:#ccc;}

.score{
position:fixed;
bottom:40px;
left:20px;
padding:8px 12px;
background:rgba(0,0,0,0.6);
border-radius:8px;
font-size:15px;
font-weight:700;
letter-spacing:1px;
color:#ccc;
}

#playersBoard{
display:none;
position:fixed;
top:20px;
left:50%;
transform:translateX(-50%);
gap:40px;
padding:8px 12px;
background:rgba(0,0,0,0.6);
border-radius:12px;
font-size:16px;
font-weight:700;
letter-spacing:1px;
}

#playersBoard .X{color:#ff4d4d;}
#playersBoard .O{color:#ccc;}

#endScreen{
display:none;
flex-direction:column;
align-items:center;
justify-content:center;
text-align:center;
}

#resultText{
white-space:pre-line;
font-weight:900;
font-size:24px;
opacity:0;
transform:translateY(-20px);
transition:all .5s ease;
}

.showResult{
opacity:1 !important;
transform:translateY(0) !important;
}

.win{color:#00ff9d;text-shadow:0 0 15px #00ff9d;}
.lose{color:#ff4d4d;text-shadow:0 0 15px #ff4d4d;}
.draw{color:#f0c75e;text-shadow:0 0 15px #f0c75e;}

@keyframes shake{
0%{transform:translateX(0);}
25%{transform:translateX(-5px);}
50%{transform:translateX(5px);}
75%{transform:translateX(-5px);}
100%{transform:translateX(0);}
}

.shake{
animation:shake .6s ease;
}

</style>
</head>

<body>

<div id="startScreen">

<h1>TicTacToe</h1>

<p>Enjoy U're Fuckin Games Here..</p>

<div class="desc">
<strong>Tic Tac Toe: Adaptive Bot System</strong> is a modern strategic duel
where artificial intelligence evolves based on your performance.
Win once, and the system enters <strong>AGGRESSIVE MODE</strong>.
Draw repeatedly, and it recalibrates for balance.
Every match shapes the next.

<br><br>

<span style="opacity:.6;font-size:12px;">
Created by : mada arf.9
</span>
</div>

<button id="startBtn">ENTER FUCKIN ARENA..</button>

</div>

<div id="playersBoard"></div>

<div id="board"></div>

<div class="score" id="scoreBoard"></div>

<div id="endScreen">
<h1 id="resultText"></h1>
<button onclick="resetGame()">NEXT FUCKIN ROUND..</button>
</div>

<script>

const boardElement=document.getElementById("board")
const scoreBoard=document.getElementById("scoreBoard")
const playersBoard=document.getElementById("playersBoard")
const endScreen=document.getElementById("endScreen")
const resultText=document.getElementById("resultText")
const startBtn=document.getElementById("startBtn")

let board=Array(9).fill("")
let gameActive=false
let drawCount=0
let aiMode="hard"
let totalScore=0,totalLoss=0,totalDraw=0
let inputBlocked=false
let tempX=0,tempO=0

function updateScore(){

scoreBoard.innerText=
`Total Score: ${totalScore} | Total Loss: ${totalLoss} | Total Draw: ${totalDraw}
Bot Mode: ${aiMode}`

let playerText='Players : '
let botText='Main Bot : '

for(let i=0;i<tempX;i++)playerText+='X'
for(let i=0;i<tempO;i++)botText+='O'

playersBoard.innerHTML=
`<span class="X">${playerText}</span> <span class="O">${botText}</span>`

playersBoard.style.display='flex'
}

function startGame(){

document.getElementById("startScreen").style.display="none"

board=Array(9).fill("")
gameActive=true
inputBlocked=false

renderBoard()
updateScore()
}

function resetGame(){

board=Array(9).fill("")
gameActive=true
inputBlocked=false

endScreen.style.display="none"

renderBoard()
updateScore()
}

function renderBoard(){

boardElement.innerHTML=""

board.forEach((val,i)=>{

const cell=document.createElement("div")

cell.className="cell"

if(val==="X")cell.classList.add("X")
if(val==="O")cell.classList.add("O")

cell.innerText=val
cell.onclick=()=>playerMove(i)

boardElement.appendChild(cell)

})

updateScore()
}

function playerMove(i){

if(!gameActive||board[i]!=""||inputBlocked)return

board[i]="X"

renderBoard()

if(checkWinner()||checkFull())return endGame()

inputBlocked=true

botMove()
}

function botMove(){

const delay=Math.floor(Math.random()*1000)+1000

setTimeout(()=>{

let move=bestMove()

if(move!==undefined)board[move]="O"

renderBoard()

inputBlocked=false

if(checkWinner()||checkFull())endGame()

},delay)

}

function bestMove(){

let empty=board.map((v,i)=>v==""?i:null).filter(v=>v!==null)

for(let i of empty){

board[i]="O"

if(getWinner(board)=="O"){board[i]="";return i}

board[i]=""

}

for(let i of empty){

board[i]="X"

if(getWinner(board)=="X"){board[i]="";return i}

board[i]=""

}

if(board[4]=="")return 4

return empty[Math.floor(Math.random()*empty.length)]

}

function getWinner(b){

const lines=[[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]]

for(let l of lines){

if(b[l[0]]&&b[l[0]]==b[l[1]]&&b[l[1]]==b[l[2]])

return b[l[0]]

}

return null
}

function checkWinner(){return getWinner(board)!=null}

function checkFull(){return board.every(c=>c!="")}

function endGame(){

gameActive=false

let winner=getWinner(board)

resultText.className=""

if(winner=="X"){

totalScore++
resultText.innerText="VICTORY"
resultText.classList.add("win")

}

else if(winner=="O"){

totalLoss++
resultText.innerText="DEFEAT"
resultText.classList.add("lose")

}

else{

totalDraw++
resultText.innerText="DRAW"
resultText.classList.add("draw")

}

resultText.classList.add("showResult")

endScreen.style.display="flex"

}

startBtn.addEventListener("click",startGame)

document.addEventListener("keydown",e=>{

if(e.key==="Enter" && document.getElementById("startScreen").style.display!=="none")

startGame()

})

</script>

</body>
</html>
