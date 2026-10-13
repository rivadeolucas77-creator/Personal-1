<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>Control Personal - Encargado</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.8.2/jspdf.plugin.autotable.min.js"></script>
<style>
*{box-sizing:border-box;font-family:system-ui, sans-serif}
body{margin:0;background:#f1f5f9;color:#0f172a}
header{background:#0f172a;color:white;padding:14px 18px;display:flex;justify-content:space-between;align-items:center;position:sticky;top:0;z-index:10;flex-wrap:wrap;gap:8px}
header h1{margin:0;font-size:17px}
nav{display:flex;gap:8px;flex-wrap:wrap;padding:10px;background:white;position:sticky;top:58px;z-index:9;border-bottom:1px solid #e2e8f0}
nav button{border:1px solid #cbd5e1;background:#f8fafc;padding:8px 12px;border-radius:999px;cursor:pointer;font-weight:600;font-size:13px}
nav button.active{background:#0f172a;color:white}
.container{max-width:1100px;margin:16px auto;padding:0 14px 60px}
.card{background:white;border-radius:16px;padding:16px;box-shadow:0 4px 14px rgba(0,0,0,.06);margin-bottom:16px}
input,select,textarea,button{width:100%;padding:10px 12px;border-radius:10px;border:1px solid #cbd5e1;margin-top:6px;font-size:14px}
.btn{background:#0f172a;color:white;border:none;cursor:pointer;font-weight:700;margin-top:10px}
.btn.green{background:#16a34a}
.btn.red{background:#dc2626}
.btn.orange{background:#ea580c}
.badge{display:inline-block;padding:3px 9px;border-radius:999px;font-size:12px;font-weight:700}
.b-pend{background:#fef3c7;color:#92400e}
.b-comp{background:#dcfce7;color:#166534}
table{width:100%;border-collapse:collapse;font-size:13px}
th,td{padding:7px;border-bottom:1px solid #e2e8f0;text-align:left}
img.thumb{width:80px;height:80px;object-fit:cover;border-radius:10px}
.small{font-size:12px;color:#64748b}
.temp-alert{color:#dc2626;font-weight:700}
.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:14px}
#login{position:fixed;inset:0;background:#0f172a;z-index:100;display:flex;align-items:center;justify-content:center;padding:20px}
#login.box{background:white;border-radius:20px;padding:24px;width:100%;max-width:380px;text-align:center}
.chip{display:inline-flex;align-items:center;gap:6px;background:#f1f5f9;border:1px solid #cbd5e1;padding:6px 10px;border-radius:999px;margin:4px 4px 0 0;font-size:13px}
.chip button{width:auto;padding:2px 8px;margin:0;border:none;background:#dc2626;color:white;border-radius:999px;cursor:pointer;font-size:12px}
</style>
</head>
<body>
<div id="login">
<div class="box">
<h2>🔐 Solo Encargado</h2>
<p class="small">Esta app la controla solo el encargado. Ingresa el PIN para administrar.</p>
<label>PIN</label>
<input type="password" id="pin" placeholder="PIN: 1234" style="text-align:center;font-size:22px;letter-spacing:6px">
<button class="btn" onclick="checkPin()">Entrar</button>
<p id="pin-error" style="color:#dc2626;font-weight:700;min-height:18px"></p>
</div>
</div>
<header>
<h1>🥩 Control Personal</h1>
<div style="display:flex;gap:8px">
<button onclick="changePin()" style="width:auto;background:#334155;color:white;border:none;padding:7px 12px;border-radius:8px;cursor:pointer;font-size:12px">Cambiar PIN</button>
<button onclick="exportData()" style="width:auto;background:#16a34a;color:white;border:none;padding:7px 12px;border-radius:8px;cursor:pointer;font-size:12px">⬇ Backup</button>
</div>
</header>
<nav>
<button onclick="show('registro')" id="n-registro" class="active">🕒 Horarios</button>
<button onclick="show('tareas')" id="n-tareas">✅ Tareas</button>
<button onclick="show('temperaturas')" id="n-temperaturas">🌡️ Temperaturas</button>
<button onclick="show('empleados')" id="n-empleados">👥 Empleados</button>
<button onclick="show('informes')" id="n-informes">📊 Informes</button>
<button onclick="show('ajustes')" id="n-ajustes">⚙️ Ajustes</button>
</nav>
<div class="container">
<section id="s-registro">
<div class="card">
<h3>Registrar Entrada / Salida</h3>
<label>Empleado</label><select id="att-emp"></select>
<div class="grid">
<div><label>Fecha</label><input type="date" id="att-fecha"></div>
<div><label>Hora Entrada</label><input type="time" id="att-entrada"></div>
<div><label>Hora Salida</label><input type="time" id="att-salida"></div>
</div>
<button class="btn" onclick="addAttendance()">Guardar Registro</button>
</div>
<div class="card">
<h3>Historial de Horarios</h3>
<table><tr><th>Empleado</th><th>Fecha</th><th>Entrada</th><th>Salida</th><th>Horas</th></tr><tbody id="att-table"></tbody></table>
<button class="btn orange" onclick="exportHorariosPDF()">📄 Exportar Horarios en PDF</button>
</div>
</section>
<section id="s-tareas" style="display:none">
<div class="card">
<h3>Asignar Tarea (Solo Encargado)</h3>
<label>Título</label><input id="task-titulo" placeholder="Ej: Sacar foto batea">
<label>Sector</label><select id="task-sector"></select>
<label>Asignar a</label><select id="task-emp"></select>
<label>Descripción</label><textarea id="task-desc" rows="2"></textarea>
<button class="btn" onclick="addTask()">Asignar Tarea</button>
</div>
<div class="card">
<h3>Tareas - Con Foto de Respaldo</h3>
<div id="task-list" class="grid"></div>
</div>
<div class="card">
<h3>Completar Tarea con Foto</h3>
<label>Seleccionar Tarea Pendiente</label><select id="comp-task"></select>
<label>Foto Evidencia</label><input type="file" id="comp-photo" accept="image/*" capture="environment">
<button class="btn green" onclick="completeTask()">Marcar como Completada</button>
</div>
</section>
<section id="s-temperaturas" style="display:none">
<div class="card">
<h3>Control de Temperatura</h3>
<div class="grid">
<div><label>Equipo</label><select id="temp-equipo"></select></div>
<div><label>Temperatura °C</label><input type="number" step="0.1" id="temp-valor" placeholder="Ej: 3.5"></div>
<div><label>Responsable</label><select id="temp-emp"></select></div>
</div>
<label>Observación</label><input id="temp-obs" placeholder="Ej: puerta bien cerrada">
<button class="btn" onclick="addTemp()">Guardar Temperatura</button>
</div>
<div class="card">
<h3>Historial Temperaturas</h3>
<table><tr><th>Fecha/Hora</th><th>Equipo</th><th>Temp</th><th>Estado</th><th>Responsable</th></tr><tbody id="temp-table"></tbody></table>
<button class="btn red" onclick="exportTempsPDF()">📄 Exportar Temperaturas en PDF</button>
</div>
</section>
<section id="s-empleados" style="display:none">
<div class="card">
<h3>Empleados Cargados</h3>
<table><tr><th>Nombre</th><th>Sector</th><th>Tareas OK</th><th>% Avance</th></tr><tbody id="emp-table"></tbody></table>
</div>
<div class="card">
<h3>Agregar Empleado</h3>
<label>Nombre</label><input id="emp-nombre" placeholder="Nombre completo">
<label>Sector</label><select id="emp-sector"></select>
<button class="btn" onclick="addEmployee()">Agregar</button>
</div>
</section>
<section id="s-informes" style="display:none">
<div class="card">
<h3>Informe por Empleado (Incluye Fotos)</h3>
<label>Seleccionar Empleado</label><select id="rep-emp" onchange="renderReport()"></select>
<div id="rep-resume" style="margin-top:12px"></div>
<div class="grid">
<div class="card"><canvas id="chartTareas"></canvas></div>
<div class="card"><canvas id="chartHoras"></canvas></div>
</div>
<div id="rep-fotos" style="margin-top:10px"></div>
<button class="btn" onclick="exportInformePDF()">📄 Exportar Este Informe en PDF con Fotos</button>
<button class="btn green" onclick="shareInformePDF()">📤 Compartir por WhatsApp</button>
</div>
<div class="card">
<h3>Ranking General</h3>
<table><tr><th>Empleado</th><th>Sector</th><th>Completadas</th><th>% Avance</th><th>Horas</th></tr><tbody id="ranking-table"></tbody></table>
<button class="btn" onclick="exportRankingPDF()">📄 Exportar Ranking General en PDF</button>
</div>
</section>
<section id="s-ajustes" style="display:none">
<div class="card">
<h3>⚙️ Agregar Items Personalizados</h3>
<p class="small">Desde aquí el encargado puede agregar nuevos sectores, equipos y más.</p>
<label>Nuevo Sector</label>
<input id="new-sector" placeholder="Ej: Sector Verdulería">
<button class="btn" onclick="addSector()">＋ Agregar Sector</button>
<div style="margin-top:12px"><b>Sectores actuales:</b><div id="list-sectores"></div></div>
<hr style="margin:18px 0">
<label>Nuevo Equipo / Cámara / Heladera</label>
<input id="new-equipo" placeholder="Ej: Freezer 2">
<button class="btn green" onclick="addEquipo()">＋ Agregar Equipo</button>
<div style="margin-top:12px"><b>Equipos actuales:</b><div id="list-equipos"></div></div>
</div>
</section>
</div>
<script>
let defaultEmployees = [
{id:1,name:"Matias Rojas",sector:"Sector Derecho"},
{id:2,name:"Ignacio Rodriguez",sector:"Sector Derecho"},
{id:3,name:"Luciano Craba",sector:"Sector Medio"},
{id:4,name:"Manuel Gonzalez",sector:"Sector HPC"},
{id:5,name:"Walter Villafañe",sector:"Sector Camara"},
{id:6,name:"Jonathan Valenzuela",sector:"Sector Caja"}
];
let defaultSectores = ["Sector Derecho","Sector Medio","Sector HPC","Sector Camara","Sector Caja","Fiambrera"];
let defaultEquipos = ["Heladera","Batea","Cámara de Frío","Cámara de Congelados"];
let db = JSON.parse(localStorage.getItem('controlDB_v3')||'null');
if(!db){ db = {employees:defaultEmployees,tasks:[],attendance:[],temps:[],sectores:defaultSectores,equipos:defaultEquipos,tipos:[]}; saveV3(); }
function saveV3(){localStorage.setItem('controlDB_v3',JSON.stringify(db))}
function save(){saveV3()}
function show(s){['registro','tareas','temperaturas','empleados','informes','ajustes'].forEach(k=>{document.getElementById('s-'+k).style.display=k===s?'block':'none';document.getElementById('n-'+k).classList.toggle('active',k===s)}); if(s==='informes') renderReport(); if(s==='ajustes') renderAjustes(); }
function checkPin(){
  const pin=document.getElementById('pin').value;
  const saved=localStorage.getItem('adminPIN')||'1234';
  if(pin===saved){ document.getElementById('login').style.display='none'; }
  else { document.getElementById('pin-error').innerText='PIN incorrecto'; }
}
function changePin(){
  const n=prompt('Nuevo PIN (min 4 digitos):', localStorage.getItem('adminPIN')||'1234');
  if(n && n.length>=4){ localStorage.setItem('adminPIN', n); alert('PIN actualizado a: '+n); }
}
function exportData(){
  const blob=new Blob([JSON.stringify(db,null,2)],{type:'application/json'});
  const url=URL.createObjectURL(blob);
  const a=document.createElement('a'); a.href=url; a.download='control_personal_backup.json'; a.click();
}
function refreshSelects(){
  const secOpts = db.sectores.map(s=>`<option>${s}</option>`).join('');
  const eqOpts = db.equipos.map(s=>`<option>${s}</option>`).join('');
  const empOpts = db.employees.map(e=>`<option value="${e.id}">${e.name} - ${e.sector}</option>`).join('');
  document.getElementById('task-sector').innerHTML=secOpts;
  document.getElementById('emp-sector').innerHTML=secOpts;
  document.getElementById('temp-equipo').innerHTML=eqOpts;
  ['att-emp','task-emp','temp-emp','rep-emp'].forEach(id=>{const el=document.getElementById(id); if(el) el.innerHTML=empOpts;});
  const ct=document.getElementById('comp-task'); if(ct) ct.innerHTML = db.tasks.filter(t=>t.status!=='completada').map(t=>`<option value="${t.id}">${t.titulo} (${t.sector})</option>`).join('');
  renderAll();
}
function renderAjustes(){
  document.getElementById('list-sectores').innerHTML = db.sectores.map((s,i)=>`<span class="chip">${s} <button onclick="delSector(${i})">x</button></span>`).join('');
  document.getElementById('list-equipos').innerHTML = db.equipos.map((s,i)=>`<span class="chip">${s} <button onclick="delEquipo(${i})">x</button></span>`).join('');
}
function addSector(){
  const v=document.getElementById('new-sector').value.trim(); if(!v) return alert('Escribe un sector');
  if(!db.sectores.includes(v)) db.sectores.push(v);
  document.getElementById('new-sector').value=''; save(); refreshSelects(); renderAjustes();
}
function delSector(i){ if(confirm('Eliminar sector '+db.sectores[i]+'?')){ db.sectores.splice(i,1); save(); refreshSelects(); renderAjustes(); } }
function addEquipo(){
  const v=document.getElementById('new-equipo').value.trim(); if(!v) return alert('Escribe un equipo');
  if(!db.equipos.includes(v)) db.equipos.push(v);
  document.getElementById('new-equipo').value=''; save(); refreshSelects(); renderAjustes();
}
function delEquipo(i){ if(confirm('Eliminar equipo '+db.equipos[i]+'?')){ db.equipos.splice(i,1); save(); refreshSelects(); renderAjustes(); } }
function addEmployee(){
  const name=document.getElementById('emp-nombre').value.trim(); if(!name) return alert('Nombre requerido');
  const sector=document.getElementById('emp-sector').value;
  db.employees.push({id:Date.now(),name,sector}); save(); refreshSelects();
}
function addAttendance(){
  const empId=+document.getElementById('att-emp').value;
  const fecha=document.getElementById('att-fecha').value || new Date().toISOString().slice(0,10);
  const entrada=document.getElementById('att-entrada').value;
  const salida=document.getElementById('att-salida').value;
  if(!entrada) return alert('Falta hora entrada');
  db.attendance.push({id:Date.now(),empId,fecha,entrada,salida}); save(); renderAll();
}
function addTask(){
  const titulo=document.getElementById('task-titulo').value.trim(); if(!titulo) return alert('Título requerido');
  const sector=document.getElementById('task-sector').value;
  const empId=+document.getElementById('task-emp').value;
  const desc=document.getElementById('task-desc').value;
  db.tasks.push({id:Date.now(),titulo,sector,empId,desc,status:'pendiente',photo:null,createdAt:new Date().toLocaleString()});
  save(); refreshSelects(); document.getElementById('task-titulo').value=''; document.getElementById('task-desc').value='';
}
function completeTask(){
  const taskId=+document.getElementById('comp-task').value; if(!taskId) return alert('No hay tareas pendientes');
  const file=document.getElementById('comp-photo').files[0];
  const task=db.tasks.find(t=>t.id===taskId);
  const done=(photoData)=>{
    task.status='completada'; task.photo=photoData||null; task.completedAt=new Date().toLocaleString(); save(); refreshSelects(); alert('Tarea completada con respaldo!');
  };
  if(file){ const r=new FileReader(); r.onload=e=>done(e.target.result); r.readAsDataURL(file);} else done(null);
}
function addTemp(){
  const equipo=document.getElementById('temp-equipo').value;
  const valor=parseFloat(document.getElementById('temp-valor').value); if(isNaN(valor)) return alert('Ingresa temperatura');
  const empId=+document.getElementById('temp-emp').value;
  const obs=document.getElementById('temp-obs').value;
  db.temps.push({id:Date.now(),equipo,valor,empId,obs,fecha:new Date().toLocaleString()}); save(); renderAll(); document.getElementById('temp-valor').value=''; document.getElementById('temp-obs').value='';
}
function tempStatus(equipo,valor){
  if(equipo.toLowerCase().includes('congelad')) return valor <= -18? {t:'OK',ok:true} : {t:'ALERTA',ok:false};
  return (valor>=0 && valor<=5)? {t:'OK',ok:true} : {t:'ALERTA',ok:false};
}
function hoursBetween(a,b){ if(!a||!b) return 0; const [h1,m1]=a.split(':').map(Number); const [h2,m2]=b.split(':').map(Number); let d=(h2+m2/60)-(h1+m1/60); if(d<0) d+=24; return Math.round(d*10)/10; }
function renderAll(){
  document.getElementById('att-table').innerHTML = db.attendance.slice().reverse().map(r=>{
    const emp=db.employees.find(e=>e.id===r.empId)?.name||'';
    return `<tr><td>${emp}</td><td>${r.fecha}</td><td>${r.entrada}</td><td>${r.salida||'-'}</td><td>${hoursBetween(r.entrada,r.salida)} hs</td></tr>`
  }).join('');
  document.getElementById('task-list').innerHTML = db.tasks.slice().reverse().map(t=>{
    const emp=db.employees.find(e=>e.id===t.empId)?.name||'';
    const badge=t.status==='completada'?'<span class="badge b-comp">Completada</span>':'<span class="badge b-pend">Pendiente</span>';
    return `<div class="card" style="margin:0"><b>${t.titulo}</b> ${badge}<br><span class="small">${t.sector} | ${emp} | ${t.createdAt}</span><p>${t.desc||''}</p>${t.photo?`<img src="${t.photo}" class="thumb">`:''}${t.completedAt?`<p class="small">Completada: ${t.completedAt}</p>`:''}</div>`
  }).join('') || '<p class="small">Sin tareas</p>';
  document.getElementById('temp-table').innerHTML = db.temps.slice().reverse().map(r=>{
    const emp=db.employees.find(e=>e.id===r.empId)?.name||''; const st=tempStatus(r.equipo,r.valor);
    return `<tr><td>${r.fecha}</td><td>${r.equipo}</td><td>${r.valor}°C</td><td class="${st.ok?'':'temp-alert'}">${st.t}</td><td>${emp}</td></tr>`
  }).join('');
  document.getElementById('emp-table').innerHTML = db.employees.map(e=>{
    const tasks=db.tasks.filter(t=>t.empId===e.id); const comp=tasks.filter(t=>t.status==='completada').length;
    const pct=tasks.length?Math.round(comp/tasks.length*100):0;
    return `<tr><td>${e.name}</td><td>${e.sector}</td><td>${comp}/${tasks.length}</td><td>${pct}%</td></tr>`
  }).join('');
  document.getElementById('ranking-table').innerHTML = db.employees.map(e=>{
    const tasks=db.tasks.filter(t=>t.empId===e.id); const comp=tasks.filter(t=>t.status==='completada').length;
    const pct=tasks.length?Math.round(comp/tasks.length*100):0;
    const hrs=db.attendance.filter(a=>a.empId===e.id).reduce((s,a)=>s+hoursBetween(a.entrada,a.salida),0);
    return `<tr><td>${e.name}</td><td>${e.sector}</td><td>${comp}</td><td>${pct}%</td><td>${hrs} hs</td></tr>`
  }).join('');
}
let chart1,chart2;
function renderReport(){
  const empId=+document.getElementById('rep-emp').value; if(!empId) return;
  const emp=db.employees.find(e=>e.id===empId);
  const tasks=db.tasks.filter(t=>t.empId===empId);
  const comp=tasks.filter(t=>t.status==='completada').length;
  const pct=tasks.length?Math.round(comp/tasks.length*100):0;
  const hrs=db.attendance.filter(a=>a.empId===empId).reduce((s,a)=>s+hoursBetween(a.entrada,a.salida),0);
  const temps=db.temps.filter(t=>t.empId===empId).length;
  document.getElementById('rep-resume').innerHTML=`<div class="card" style="background:#f8fafc"><b>${emp.name}</b> - ${emp.sector}<br>Tareas: ${comp}/${tasks.length} (${pct}%)<br>Horas: ${hrs} hs | Temperaturas: ${temps}</div>`;
  const fotosConRespaldo = tasks.filter(t=>t.photo);
  document.getElementById('rep-fotos').innerHTML = fotosConRespaldo.length? `<b>Evidencias fotográficas (${fotosConRespaldo.length}):</b><div style="display:flex;flex-wrap:wrap;gap:8px;margin-top:8px">${fotosConRespaldo.map(t=>`<div style="text-align:center"><img src="${t.photo}" style="width:100px;height:100px;object-fit:cover;border-radius:10px;border:1px solid #e2e8f0"><div class="small">${t.titulo}</div></div>`).join('')}</div>` : '<p class="small">Sin fotos cargadas todavía</p>';
  if(chart1) chart1.destroy(); if(chart2) chart2.destroy();
  chart1=new Chart(document.getElementById('chartTareas'),{type:'doughnut',data:{labels:['Completadas','Pendientes'],datasets:[{data:[comp,tasks.length-comp],backgroundColor:['#16a34a','#e2e8f0']}]}});
  const byDay={}; db.attendance.filter(a=>a.empId===empId).forEach(a=>byDay[a.fecha]=hoursBetween(a.entrada,a.salida));
  chart2=new Chart(document.getElementById('chartHoras'),{type:'bar',data:{labels:Object.keys(byDay),datasets:[{label:'Horas trabajadas',data:Object.values(byDay),backgroundColor:'#0f172a'}]}});
}
function getEmpStats(empId){
  const tasks=db.tasks.filter(t=>t.empId===empId);
  const comp=tasks.filter(t=>t.status==='completada').length;
  const pct=tasks.length?Math.round(comp/tasks.length*100):0;
  const hrs=db.attendance.filter(a=>a.empId===empId).reduce((s,a)=>s+hoursBetween(a.entrada,a.salida),0);
  const temps=db.temps.filter(t=>t.empId===empId).length;
  return {tasks,comp,pct,hrs,temps};
}
function exportInformePDF(){
  const empId=+document.getElementById('rep-emp').value;
  const emp=db.employees.find(e=>e.id===empId); if(!emp) return alert('Selecciona empleado');
  const s=getEmpStats(empId);
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF();
  doc.setFontSize(16); doc.text('Informe de Empleado', 14, 18);
  doc.setFontSize(12);
  doc.text(`Nombre: ${emp.name}`, 14, 28);
  doc.text(`Sector: ${emp.sector}`, 14, 35);
  doc.text(`Tareas completadas: ${s.comp}/${s.tasks.length} (${s.pct}%)`, 14, 42);
  doc.text(`Horas registradas: ${s.hrs} hs`, 14, 49);
  doc.text(`Temperaturas tomadas: ${s.temps}`, 14, 56);
  doc.text(`Fecha de informe: ${new Date().toLocaleString('es-AR')}`, 14, 63);
  if(s.tasks.length>0){
    doc.autoTable({ startY: 70, head: [['Tarea','Sector','Estado','Fecha','Foto']], body: s.tasks.map(t=>[t.titulo, t.sector, t.status, t.createdAt, t.photo? 'SI' : 'NO']) });
  }
  const att=db.attendance.filter(a=>a.empId===empId);
  if(att.length>0){
    doc.autoTable({ startY: doc.lastAutoTable? doc.lastAutoTable.finalY+10 : 70, head: [['Fecha','Entrada','Salida','Horas']], body: att.map(a=>[a.fecha, a.entrada, a.salida||'-', hoursBetween(a.entrada,a.salida)]) });
  }
  const tareasConFoto = s.tasks.filter(t=>t.photo);
  if(tareasConFoto.length>0){
    doc.addPage();
    doc.setFontSize(14); doc.text('Evidencias Fotograficas', 14, 18);
    let y = 25;
    tareasConFoto.forEach((t, idx)=>{
      if(y > 230){
        doc.addPage();
        y = 15;
      }
      doc.setFontSize(11);
      doc.text(`${idx+1}. ${t.titulo} - ${t.sector} (${t.completedAt||t.createdAt})`, 14, y);
      y += 6;
      try{
        const isPng = t.photo.includes('image/png');
        doc.addImage(t.photo, isPng? 'PNG' : 'JPEG', 14, y, 60, 60);
        y += 65;
      }catch(e){
        doc.text('[No se pudo incluir la foto]', 14, y);
        y += 10;
      }
    });
  }
  doc.save(`Informe_${emp.name.replace(/ /g,'_')}.pdf`);
}
function exportRankingPDF(){
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF();
  doc.setFontSize(16); doc.text('Ranking General - Control Personal', 14, 18);
  doc.setFontSize(11); doc.text(`Fecha: ${new Date().toLocaleString('es-AR')}`, 14, 25);
  const rows=db.employees.map(e=>{
    const s=getEmpStats(e.id);
    return [e.name, e.sector, `${s.comp}/${s.tasks.length}`, s.pct+'%', s.hrs+' hs', s.temps];
  });
  doc.autoTable({ startY: 30, head: [['Empleado','Sector','Tareas','% Avance','Horas','Temps']], body: rows });
  doc.save('Ranking_General.pdf');
}
function exportTempsPDF(){
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF();
  doc.setFontSize(16); doc.text('Control de Temperaturas', 14, 18);
  const rows=db.temps.map(r=>{
    const emp=db.employees.find(e=>e.id===r.empId)?.name||'';
    const st=tempStatus(r.equipo,r.valor).t;
    return [r.fecha, r.equipo, r.valor+'°C', st, emp, r.obs||''];
  });
  doc.autoTable({ startY: 25, head: [['Fecha','Equipo','Temp','Estado','Responsable','Obs']], body: rows });
  doc.save('Temperaturas.pdf');
}
function exportHorariosPDF(){
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF();
  doc.setFontSize(16); doc.text('Registro de Horarios - Entrada y Salida', 14, 18);
  const rows=db.attendance.map(r=>{
    const emp=db.employees.find(e=>e.id===r.empId)?.name||'';
    return [emp, r.fecha, r.entrada, r.salida||'-', hoursBetween(r.entrada,r.salida)+' hs'];
  });
  doc.autoTable({ startY: 25, head: [['Empleado','Fecha','Entrada','Salida','Horas']], body: rows });
  doc.save('Horarios.pdf');
}
function shareInformePDF(){
  const empId=+document.getElementById('rep-emp').value;
  const emp=db.employees.find(e=>e.id===empId); if(!emp) return alert('Selecciona empleado');
  const s=getEmpStats(empId);
  exportInformePDF();
  const texto=`Informe de ${emp.name} (${emp.sector}): ${s.comp}/${s.tasks.length} tareas (${s.pct}%), ${s.hrs} hs, ${s.temps} temperaturas.`;
  const url=`https://wa.me/?text=${encodeURIComponent(texto)}`;
  window.open(url,'_blank');
}
document.getElementById('att-fecha').value=new Date().toISOString().slice(0,10);
refreshSelects();
</script>
</body>
</html>
