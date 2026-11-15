# GG.
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>My Planner</title>
<style>
*{box-sizing:border-box}
body{margin:0;font-family:-apple-system,BlinkMacSystemFont,sans-serif;background:#fff6fb;color:#392d43}
header{background:linear-gradient(135deg,#e9c9f5,#f8d5e7);padding:25px 20px 18px;border-radius:0 0 28px 28px}
h1{margin:0;font-size:27px}
header p{margin:6px 0;color:#705675}
nav{display:flex;gap:6px;justify-content:space-around;padding:12px 5px;background:white;position:sticky;top:0;z-index:2}
nav button{border:0;background:none;padding:10px 5px;border-radius:12px;font-size:12px;color:#755d80}
nav button.active{background:#f2def8;color:#753d91;font-weight:bold}
main{padding:15px;max-width:600px;margin:auto}
.page{display:none}.page.active{display:block}
.card{background:white;border-radius:20px;padding:17px;margin:12px 0;box-shadow:0 3px 12px #e8d9e9}
h2{font-size:20px;margin:5px 0 15px}
h3{margin:4px 0 10px;font-size:16px}
label{display:flex;align-items:center;gap:10px;padding:10px 0;font-size:14px}
input[type=checkbox]{width:20px;height:20px;accent-color:#bd82d4}
select,input[type=text],textarea{width:100%;padding:12px;border:1px solid #e5d4eb;border-radius:12px;background:#fffaff;font:inherit;margin:6px 0}
textarea{min-height:100px}
button.action{background:#bd82d4;color:white;border:0;border-radius:12px;padding:12px 16px;font-weight:bold;margin:5px 0}
.progress{height:9px;background:#f0e5f4;border-radius:10px;overflow:hidden}
.bar{height:100%;background:#c38cdb;width:0}
.small{color:#927b9a;font-size:12px}
.day{border-top:1px solid #f0e7f3;padding:12px 0}
footer{text-align:center;color:#a58cae;font-size:12px;padding:25px}
</style>
</head>
<body>
<header>
<h1>My Planner ✨</h1>
<p>My weeks • My riding • My progress</p>
</header>

<nav>
<button class="active" onclick="showPage('week',this)">📅 Week</button>
<button onclick="showPage('rider',this)">🐴 Rider</button>
<button onclick="showPage('progress',this)">⭐ Progress</button>
<button onclick="showPage('notes',this)">📝 Notes</button>
</nav>

<main>
<section id="week" class="page active">
<div class="card">
<h2>My Week 💗</h2>
<select id="weekChoice" onchange="changeWeek()">
<option value="Mum">Mum Week</option>
<option value="Dad">Dad Week</option>
</select>
<div class="progress"><div class="bar" id="weekBar"></div></div>
<p class="small" id="weekCount">0 completed</p>
</div>
<div id="days"></div>
</section>

<section id="rider" class="page">
<div class="card">
<h2>Rider Training 🐎</h2>
<p class="small">Tick each activity when completed.</p>
</div>
<div id="workouts"></div>
<div class="card">
<h3>Rider reflection</h3>
<textarea id="reflection" placeholder="How did riding feel today?"></textarea>
<button class="action" onclick="saveReflection()">Save reflection</button>
</div>
</section>

<section id="progress" class="page">
<div class="card">
<h2>My Progress ⭐</h2>
<p>Week <span id="weekNumber">1</span> of 12</p>
<div class="progress"><div class="bar" id="progressBar"></div></div>
<button class="action" onclick="nextWeek()">Complete check-in</button>
<button class="action" onclick="resetProgress()">Reset progress</button>
<p class="small">Check in every week and celebrate your progress!</p>
</div>
<div class="card">
<h3>My riding goals</h3>
<label><input type="checkbox" class="goal"> Better balance</label>
<label><input type="checkbox" class="goal"> Stronger core</label>
<label><input type="checkbox" class="goal"> Better posture</label>
<label><input type="checkbox" class="goal"> More confidence</label>
</div>
</section>

<section id="notes" class="page">
<div class="card">
<h2>My Notes 📝</h2>
<textarea id="notesText" placeholder="Write anything you want to remember..."></textarea>
<button class="action" onclick="saveNotes()">Save notes</button>
<button class="action" onclick="clearTicks()">Reset weekly ticks</button>
<p class="small">Your notes stay saved on this device.</p>
</div>
</section>
<footer>Made for my life & my riding 💗</footer>
</main>

<script>
const mum={
Monday:["School","Homework"],
Tuesday:["School","Dance","Arni time"],
Wednesday:["School","Homework"],
Thursday:["School","Dance","Arni time"],
Friday:["School","Arni afternoon"],
Saturday:["Arni time","Riding"],
Sunday:["Arni time","Riding","Get ready for school"]
};
const dad={
Monday:["School","Homework"],
Tuesday:["School","Arni time"],
Wednesday:["School","Homework"],
Thursday:["School","Arni time"],
Friday:["School","Arni afternoon"],
Saturday:["Arni time","Riding"],
Sunday:["Arni time","Riding","Get ready for school"]
};
const training={
Monday:["Core & posture","Dead bug","Bird dog","Plank","Stretch"],
Tuesday:["Leg control","Squats","Reverse lunges","Calf raises","Stretch"],
Wednesday:["Recovery","Gentle mobility","Easy walk"],
Thursday:["Balance","Single-leg balance","Marching","Bird dog"],
Friday:["Mobility","Hip mobility","Shoulder mobility","Stretch"],
Saturday:["Full body","Squats","Dead bug","Plank","Stretch"],
Sunday:["Recovery","Gentle stretching","Rest"]
};
function showPage(id,btn){
document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
document.getElementById(id).classList.add('active');
document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
btn.classList.add('active');
}
function getTicks(){return JSON.parse(localStorage.getItem('ticks')||'{}')}
function setTicks(x){localStorage.setItem('ticks',JSON.stringify(x))}
function changeWeek(){renderDays()}
function renderDays(){
let week=document.getElementById('weekChoice').value;
let data=week==='Mum'?mum:dad;
let ticks=getTicks(),html='',total=0,done=0;
for(let day in data){
html+=`<div class="card"><h3>${day}</h3>`;
data[day].forEach(task=>{
let key=week+'-'+day+'-'+task;
total++;if(ticks[key])done++;
html+=`<label><input type="checkbox" ${ticks[key]?'checked':''} onchange="tick('${key}',this.checked)">${task}</label>`;
});
html+='</div>';
}
document.getElementById('days').innerHTML=html;
document.getElementById('weekBar').style.width=(total?done/total*100:0)+'%';
document.getElementById('weekCount').textContent=done+' of '+total+' completed';
}
function tick(key,value){
let t=getTicks();t[key]=value;setTicks(t);renderDays();
}
function renderWorkouts(){
let t=getTicks(),html='';
for(let day in training){
html+=`<div class="card"><h3>${day}</h3><p class="small">${training[day][0]}</p>`;
training[day].slice(1).forEach(task=>{
let key='work-'+day+'-'+task;
html+=`<label><input type="checkbox" ${t[key]?'checked':''} onchange="workTick('${key}',this.checked)">${task}</label>`;
});
html+='</div>';
}
document.getElementById('workouts').innerHTML=html;
}
function workTick(key,v){let t=getTicks();t[key]=v;setTicks(t)}
let checkin=Number(localStorage.getItem('checkin')||1);
function updateProgress(){
document.getElementById('weekNumber').textContent=checkin;
document.getElementById('progressBar').style.width=(checkin/12*100)+'%';
}
function nextWeek(){if(checkin<12)checkin++;localStorage.setItem('checkin',checkin);updateProgress()}
function resetProgress(){if(confirm('Reset your 12-week progress?')){checkin=1;localStorage.setItem('checkin',1);updateProgress()}}
function saveNotes(){localStorage.setItem('notes',document.getElementById('notesText').value);alert('Notes saved!')}
function saveReflection(){localStorage.setItem('reflection',document.getElementById('reflection').value);alert('Reflection saved!')}
function clearTicks(){if(confirm('Reset all weekly and workout ticks?')){localStorage.removeItem('ticks');renderDays();renderWorkouts()}}
document.getElementById('notesText').value=localStorage.getItem('notes')||'';
document.getElementById('reflection').value=localStorage.getItem('reflection')||'';
renderDays();renderWorkouts();updateProgress();
</script>
</body>
</html>
