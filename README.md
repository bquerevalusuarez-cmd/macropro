# macropro
applicacion para contar calorias macros, etc, entrenador, fit
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MacroPro AI Coach</title>
  <meta name="theme-color" content="#0ea5e9">
  <style>
    body{font-family:system-ui;margin:0;background:#0b1220;color:#e5e7eb}
    header{padding:16px;background:#020617;position:sticky;top:0}
    h1{margin:0;font-size:20px}
    main{padding:16px;display:grid;gap:14px}
    .card{background:#020617;border-radius:18px;padding:14px}
    label{font-size:12px;color:#9ca3af}
    input,select,textarea,button{width:100%;padding:10px;border-radius:12px;border:none;margin-top:6px}
    button{background:#0ea5e9;color:#020617;font-weight:700}
    .grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}
    .food{display:flex;gap:10px;align-items:center;margin-top:8px}
    .food img{width:48px;height:48px;border-radius:10px;object-fit:cover}
    footer{text-align:center;font-size:12px;color:#9ca3af;padding:12px}
  </style>
</head>
<body>

<header>
  <h1>MacroPro AI Coach — PRO</h1>
</header>

<main>

<section class="card">
<h2>Perfil & Objetivo</h2>
<div class="grid">
  <div><label>Edad</label><input id="edad" type="number"></div>
  <div><label>Peso (kg)</label><input id="peso" type="number"></div>
  <div><label>Altura (cm)</label><input id="altura" type="number"></div>
  <div>
    <label>Objetivo</label>
    <select id="objetivo">
      <option>Definición</option>
      <option>Mantenimiento</option>
      <option>Volumen</option>
    </select>
  </div>
</div>
<button onclick="calcularMacros()">Calcular macros</button>
<p id="macroObjetivo"></p>
<p id="coach"></p>
</section>

<section class="card">
<h2>Registrar comida (con medidas)</h2>
<input id="buscar" placeholder="Buscar alimento..." oninput="filtrar()">
<div id="lista"></div>
</section>

<section class="card">
<h2>Resumen diario</h2>
<p id="resumen">Proteína: 0g | Carbohidratos: 0g | Grasas: 0g | Calorías: 0</p>
<button onclick="guardarDia()">Guardar día</button>
</section>

<section class="card">
<h2>Entrenamientos & Rutinas</h2>
<textarea id="rutina" placeholder="Ej: Pecho + tríceps, 4x10 press..."></textarea>
<button onclick="guardarRutina()">Guardar rutina</button>
<div id="rutinas"></div>
</section>

<section class="card">
<h2>Coach IA (preguntas)</h2>
<input id="pregunta" placeholder="¿Qué hago para definir?">
<button onclick="coachIA()">Preguntar</button>
<p id="respuesta"></p>
</section>

<section class="card">
<h2>Historial</h2>
<div id="historial"></div>
</section>

</main>

<footer>App privada • Gratis • Solo con link</footer>

<script>
const alimentos=[
{n:'Filete de pollo',med:'100 g',p:31,c:0,g:3,k:165,img:'https://images.unsplash.com/photo-1604908176997-125f25cc6f3d'},
{n:'Pescado filete',med:'100 g',p:22,c:0,g:2,k:120,img:'https://images.unsplash.com/photo-1544025162-d76694265947'},
{n:'Lentejas cocidas',med:'1 taza',p:18,c:40,g:1,k:230,img:'https://images.unsplash.com/photo-1615485737457-fb3df7c1b2a5'},
{n:'Frejoles cocidos',med:'1 taza',p:15,c:45,g:1,k:245,img:'https://images.unsplash.com/photo-1604908812129-9c4d7e8c1b9a'},
{n:'Arroz blanco cocido',med:'1 taza',p:4,c:45,g:0,k:200,img:'https://images.unsplash.com/photo-1586201375761-83865001e17b'},
{n:'Huevo',med:'1 unidad',p:6,c:1,g:5,k:70,img:'https://images.unsplash.com/photo-1517959105821-eaf2591984ca'},
{n:'Papa sancochada',med:'1 mediana',p:2,c:26,g:0,k:110,img:'https://images.unsplash.com/photo-1582515073490-dc84a7c3f4f8'}
];

let total={p:0,c:0,g:0,k:0};

function filtrar(){
  const q=buscar.value.toLowerCase();
  lista.innerHTML='';
  alimentos.filter(a=>a.n.toLowerCase().includes(q)).forEach(a=>{
    const d=document.createElement('div');
    d.className='food';
    d.innerHTML=`<img src="${a.img}"><div><b>${a.n}</b><br>${a.med} • ${a.k} kcal</div>`;
    d.onclick=()=>add(a);
    lista.appendChild(d);
  });
}

function add(a){
  total.p+=a.p; total.c+=a.c; total.g+=a.g; total.k+=a.k;
  resumen.innerText=`Proteína: ${total.p}g | Carbohidratos: ${total.c}g | Grasas: ${total.g}g | Calorías: ${total.k}`;
}

function calcularMacros(){
  const p=peso.value;
  const kcal=p*30;
  const prot=p*2;
  macroObjetivo.innerText=`Objetivo diario: ${Math.round(kcal)} kcal | Proteína ${Math.round(prot)} g`;
  coach.innerText=objetivo.value==='Definición'
    ? 'Coach: Déficit moderado, fuerza 4–6 días, proteína alta.'
    : objetivo.value==='Volumen'
    ? 'Coach: Superávit ligero, progresión de cargas y descanso.'
    : 'Coach: Mantén calorías y constancia.';
}

function guardarDia(){
  const h=JSON.parse(localStorage.getItem('hist')||'[]');
  h.push({fecha:new Date().toLocaleDateString(),k:total.k});
  localStorage.setItem('hist',JSON.stringify(h));
  mostrarHistorial();
}

function guardarRutina(){
  const r=JSON.parse(localStorage.getItem('rutinas')||'[]');
  r.push({fecha:new Date().toLocaleDateString(),txt:rutina.value});
  localStorage.setItem('rutinas',JSON.stringify(r));
  rutina.value='';
  mostrarRutinas();
}

function coachIA(){
  const q=pregunta.value.toLowerCase();
  respuesta.innerText=
    q.includes('definir')?'Para definir: déficit, proteína alta y fuerza.'
    :q.includes('volumen')?'Para volumen: superávit y progresión.'
    :'Entrena constante y duerme bien.';
}

function mostrarHistorial(){
  historial.innerHTML='';
  (JSON.parse(localStorage.getItem('hist')||'[]'))
  .forEach(d=>historial.innerHTML+=`<p>${d.fecha}: ${d.k} kcal</p>`);
}

function mostrarRutinas(){
  rutinas.innerHTML='';
  (JSON.parse(localStorage.getItem('rutinas')||'[]'))
  .forEach(r=>rutinas.innerHTML+=`<p>${r.fecha}: ${r.txt}</p>`);
}

mostrarHistorial(); mostrarRutinas(); filtrar();
</script>

</body>
</html>
