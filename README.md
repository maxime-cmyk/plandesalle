<!DOCTYPE html>

<html lang="fr">
<head>
<meta charset="utf-8"/>
<meta content="width=device-width,initial-scale=1" name="viewport"/>
<title>Plan de salle</title>
<style>
  :root{
    --bg:#f7f8fa;
    --panel:#ffffff;
    --ink:#1e1f24;
    --muted:#7a7f87;
    --accent:#3b82f6;
    --accent-2:#64748b;
    --ok:#16a34a;
    --warn:#f59e0b;
    --danger:#dc2626;
    --seat:#e2e8f0;
    --table:#f5f6f9;
    --grid:#eef1f6;
    --shadow: 0 8px 24px rgba(16,24,40,.06), 0 2px 8px rgba(16,24,40,.05);
  }
  html,body{height:100%}
  body{
    margin:0; font:14px/1.35 system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    color:var(--ink); background:var(--bg); overflow:hidden;
  }
  /* Layout */
  .app{display:grid; grid-template-columns: 290px 1fr; grid-template-rows: 56px 1fr; height:100%;}
  .topbar{grid-column:1/3; display:flex; align-items:center; gap:12px; background:var(--panel); padding:8px 12px; box-shadow:var(--shadow); z-index:5;}
  .brand{font-weight:600; letter-spacing:.2px; margin-right:auto;}
  .lang{display:flex; gap:6px; align-items:center;}
  .lang select{border:1px solid #e5e7eb; border-radius:8px; padding:6px 8px; background:#fff;}
  .btn{border:1px solid #e5e7eb; background:#fff; color:var(--ink); padding:8px 10px; border-radius:10px; cursor:pointer;}
  .btn.primary{background:var(--accent); color:#fff; border-color:var(--accent);}
  .btn.ghost{background:transparent;}
  .btn:disabled{opacity:.6; cursor:not-allowed;}
  .left{grid-row:2/3; grid-column:1/2; overflow:auto; background:var(--panel); border-right:1px solid #e5e7eb;}
  .section{padding:14px 14px 10px; border-bottom:1px solid #eef1f6;}
  .section h3{margin:0 0 10px; font-size:13px; font-weight:700; text-transform:uppercase; letter-spacing:.08em; color:var(--accent-2);}
  .toolrow{display:grid; grid-template-columns:1fr 1fr; gap:8px;}
  .field{display:flex; flex-direction:column; gap:6px; margin-bottom:10px;}
  .label{font-size:12px; color:var(--muted);}
  .input, .select, .checkboxes{border:1px solid #e5e7eb; border-radius:10px; background:#fff; padding:8px 10px;}
  .checkboxes{display:flex; flex-wrap:wrap; gap:8px;}
  .chip{display:inline-flex; align-items:center; gap:6px; padding:6px 8px; border-radius:999px; background:#f1f5f9; border:1px solid #e2e8f0; font-size:12px;}
  .list{display:flex; flex-direction:column; gap:6px; max-height:280px; overflow:auto;}
  .guest{display:flex; align-items:center; justify-content:space-between; gap:8px; padding:8px 10px; border:1px solid #e5e7eb; border-radius:10px; background:#fff;}
  .guest .leftx{display:flex; align-items:center; gap:10px;}
  .guest .badges{display:flex; gap:4px; flex-wrap:wrap;}
  .badge{font-size:11px; color:var(--muted);}
  .stage-wrap{position:relative; grid-row:2/3; grid-column:2/3; overflow:hidden; background:
      linear-gradient(0deg, transparent 24px, var(--grid) 25px), linear-gradient(90deg, transparent 24px, var(--grid) 25px),
      #fafbff;
      background-size:25px 25px, 25px 25px;
  }
  .hud{position:absolute; top:10px; left:10px; display:flex; gap:8px; align-items:center; background:rgba(255,255,255,.9); padding:6px 8px; border:1px solid #e5e7eb; border-radius:10px; backdrop-filter: blur(6px); z-index:4;}
  .hud input[type=range]{width:140px;}
  .legend{position:absolute; bottom:12px; left:12px; background:rgba(255,255,255,.9); border:1px solid #e5e7eb; border-radius:10px; padding:8px 10px; font-size:12px; backdrop-filter: blur(6px);}
  .minimap{position:absolute; right:12px; bottom:12px; width:190px; height:120px; background:rgba(255,255,255,.9); border:1px solid #e5e7eb; border-radius:10px; box-shadow:var(--shadow); padding:6px; z-index:4;}
  svg#stage{width:100%; height:100%;}
  .table{fill:var(--table); stroke:#cbd5e1; stroke-width:2;}
  .table.selected{stroke:var(--accent); stroke-width:3;}
  .seat{fill:var(--seat); stroke:#cbd5e1;}
  .handle{cursor:move;}
  .label-small{font-size:11px; fill:#6b7280; user-select:none;}
  .seat-emoji{font-size:18px; user-select:none; cursor:grab;}
  .seat-empty{font-size:10px; fill:#cbd5e1;}
  .obj{font-size:22px; cursor:move; user-select:none;}
  .tooltip{position:fixed; background:#111; color:#fff; padding:6px 8px; border-radius:8px; font-size:12px; pointer-events:none; z-index:10; max-width:260px; white-space:pre-wrap;}
  .prop{padding:12px 14px; display:flex; flex-direction:column; gap:8px;}
  .two{display:grid; grid-template-columns:1fr 1fr; gap:8px;}
  .muted{color:var(--muted);}
  .divider{height:1px; background:#eef1f6; margin:8px 0;}
  .danger{color:var(--danger);}
  /* Print */
  @media print {
    body{overflow:visible; background:#fff;}
    .topbar, .left, .hud, .minimap, .legend .muted{display:none !important;}
    .stage-wrap{background:#fff;}
    .legend{display:block !important; position:static; border:0; background:#fff; padding:0; margin-top:8px;}
    @page { size: A4 portrait; margin: 12mm; }
  }
</style>
</head>
<body>
<div class="app">
<div class="topbar">
<div class="brand">Plan de salle</div>
<div class="lang">
<span id="t_lang">Langue</span>
<select aria-label="Langue" id="langSel">
<option selected="" value="fr">FR</option>
<option value="en">EN</option>
<option value="nl">NL</option>
</select>
</div>
<button class="btn" id="btnExport" title="Exporter JSON">Export</button>
<label class="btn">
<input accept="application/json" id="fileImport" style="display:none" type="file"/>
<span id="t_import">Importer</span>
</label>
<button class="btn" id="btnPrint">Imprimer / PDF</button>
<div aria-live="polite" class="muted" id="saveState"></div>
<div id="adminControls" style="display:none; margin-left:12px;">
<button class="btn" onclick="lockObjects()">Figer Objets</button>
<button class="btn" onclick="unlockObjects()">Défiger Objets</button>
</div>

<button class="btn" onclick="document.getElementById('adminModal').style.display='block'">Admin</button></div>
<aside class="left" id="sidebar">
<div class="section">
<h3 id="t_add">Ajouter</h3>
<div class="toolrow">
<button class="btn primary" id="addRound">Table Rond</button>
<button class="btn primary" id="addRect">Table Rectangle</button>
</div>
<div class="toolrow" style="margin-top:8px">
<button class="btn" data-obj="buffet">🍽️ Buffet</button>
<button class="btn" data-obj="photomaton">📸 Photomaton</button>
<button class="btn" data-obj="candybar">🍬 Candy bar</button>
<button class="btn" data-obj="bar">🍺 Bar</button>
</div>
</div>
<div class="section">
<h3>Listing</h3>
<button class="btn" id="btnListing">📄 Générer le listing</button>
</div>
<div class="section">
<h3 id="t_props">Propriétés</h3>
<div class="prop muted" id="props">Sélectionnez une table ou un objet…</div>
</div>
<div class="section">
<h3 id="t_guests">Invités</h3>
<div class="field">
<label class="label" id="t_addGuest">Ajouter un invité</label>
<div class="two">
<input aria-label="Nom" class="input" id="gName" placeholder="Nom"/>
<select aria-label="Genre" class="select" id="gGender">
<option value="homme">Homme</option>
<option value="femme">Femme</option>
<option value="garçon">Garçon</option>
<option value="fille">Fille</option>
<option value="bébé">Bébé</option>
<option value="autre">Autre</option>
</select>
</div>
<div aria-label="Régimes et situations" class="checkboxes" id="dietBoxes">
<label class="chip"><input type="checkbox" value="enceinte"/> 🤰</label>
<label class="chip"><input type="checkbox" value="végétarien"/> 🍃</label>
<label class="chip"><input type="checkbox" value="végan"/> 🌿</label>
<label class="chip"><input type="checkbox" value="halal"/> 🕌</label>
<label class="chip"><input type="checkbox" value="sans_lactose"/> 🥛🚫</label>
<label class="chip"><input type="checkbox" value="sans_gluten"/> 🌾🚫</label>
</div>
<input aria-label="Allergies" class="input" id="gAllergies" placeholder="Allergies (ex: arachides, œufs…)"/>
<button class="btn" id="btnAddGuest">+ Ajouter</button>
</div>
<div class="divider"></div>
<div aria-live="polite" class="list" id="guestList"></div>
</div>
</aside>
<main class="stage-wrap">
<div class="hud">
<button aria-label="Zoom out" class="btn" id="zOut">−</button>
<input aria-label="Zoom" id="zoom" max="3" min="0.5" step="0.05" type="range" value="1"/>
<button aria-label="Zoom in" class="btn" id="zIn">+</button>
<span class="muted" id="t_hintPan">Maintenir espace pour déplacer</span>
</div>
<svg aria-label="Plan de salle" id="stage" tabindex="0" viewbox="-800 -500 1600 1000"></svg>
<div class="legend">
<div><strong>Table ronde:</strong> ○ — <strong>Rectangulaire:</strong> ▭ — <strong>Objets:</strong> 🍽️ 📸 🍬 🍺</div>
<div class="muted" style="margin-top:4px">Astuce: glisser-déposer les invités sur les sièges.</div>
</div>
<div class="minimap" id="miniWrap">
<svg height="100%" id="minimap" width="100%"></svg>
</div>
</main>
</div>
<div class="tooltip" id="tooltip" style="display:none"></div>
<script>
/* =========================
   i18n
   ========================= */
const I18N = {
  fr: {
    lang: "Langue", add:"Ajouter", props:"Propriétés", guests:"Invités", addGuest:"Ajouter un invité",
    import:"Importer", hintPan:"Maintenir espace pour déplacer", saved:"Sauvegardé", saving:"Enregistrement…",
    seats:"Places", length:"Longueur", width:"Largeur", rotate:"Rotation", delete:"Supprimer",
    round:"Table ronde", rect:"Table rect.", unassign:"Retirer", title:"Plan de salle",
    selectPrompt:"Sélectionnez une table ou un objet…", tableName:"Nom de la table"
  },
  en: {
    lang:"Language", add:"Add", props:"Properties", guests:"Guests", addGuest:"Add a guest",
    import:"Import", hintPan:"Hold Space to pan", saved:"Saved", saving:"Saving…",
    seats:"Seats", length:"Length", width:"Width", rotate:"Rotation", delete:"Delete",
    round:"Round table", rect:"Rect. table", unassign:"Unseat", title:"Seating plan",
    selectPrompt:"Select a table or an object…", tableName:"Table name"
  },
  nl: {
    lang:"Taal", add:"Toevoegen", props:"Eigenschappen", guests:"Gasten", addGuest:"Gast toevoegen",
    import:"Importeren", hintPan:"Spatie ingedrukt houden om te slepen", saved:"Opgeslagen", saving:"Opslaan…",
    seats:"Plaatsen", length:"Lengte", width:"Breedte", rotate:"Rotatie", delete:"Verwijderen",
    round:"Ronde tafel", rect:"Rechth. tafel", unassign:"Verwijderen", title:"Tafelplan",
    selectPrompt:"Selecteer een tafel of object…", tableName:"Tafelnaam"
  }
};
let LANG="fr";
function t(k){ return (I18N[LANG] && I18N[LANG][k]) || I18N.fr[k] || k; }
function applyI18N(){
  document.title=t("title");
  document.getElementById("t_lang").textContent=t("lang");
  document.getElementById("t_add").textContent=t("add");
  document.getElementById("t_props").textContent=t("props");
  document.getElementById("t_guests").textContent=t("guests");
  document.getElementById("t_addGuest").textContent=t("addGuest");
  document.querySelector("label.btn span#t_import").textContent=t("import");
  document.getElementById("t_hintPan").textContent=t("hintPan");
  const props = document.getElementById("props");
  if(!selectedId) props.textContent=t("selectPrompt");
}

/* =========================
   Data model
   ========================= */
const state = {
  version:1,
  invites:{},
  tables:{},
  objects:{},
  lastId:0,
  title:"Plan"
};
function uid(){ state.lastId++; return "id"+state.lastId; }
const EMOJI_BY_GENDER = { homme:"👨", femme:"👩", garçon:"👦", fille:"👧", bébé:"👶", autre:"🧑" };
const EMOJI_BY_OBJ = { buffet:"🍽️", photomaton:"📸", candybar:"🍬", bar:"🍺" };
const DIET_EMOJI = { enceinte:"🤰", végétarien:"🍃", végan:"🌿", halal:"🕌", sans_lactose:"🥛🚫", sans_gluten:"🌾🚫" };

/* =========================
   Geometry
   ========================= */
const MIN_SEAT_ARC = 46;
const RECT_W = 60;
const RECT_SPACING_MIN = 40;
function computeRoundGeometry(n){
  const Rseat = Math.max(30, MIN_SEAT_ARC/(2*Math.sin(Math.PI/n)));
  const Rtable = Math.max(30, Rseat - 6);
  const seats=[];
  for(let i=0;i<n;i++){
    const th = 2*Math.PI*i/n - Math.PI/2;
    seats.push({ index:i, x: Rseat*Math.cos(th), y: Rseat*Math.sin(th), angle: th+Math.PI });
  }
  return { Rseat, Rtable, seats };
}
function computeRectGeometry(n) {
  const SEAT_RADIUS = 10;
  const SEAT_PAD = 4;
  const MIN_TABLE_WIDTH = 60;
  const RECT_SPACING_MIN = 40;

  // Nombre total de sièges (au moins 1)
  const totalSeats = Math.max(1, n);

  // Répartition haut/bas
  const topCount = Math.ceil(totalSeats / 2);
  const bottomCount = Math.floor(totalSeats / 2);

  // Longueur de la table : basée sur le côté le plus long
  const maxSide = Math.max(topCount, bottomCount);
  const L = RECT_SPACING_MIN * (maxSide + 1);

  // Largeur fixe ou ajustée
  const W = Math.max(MIN_TABLE_WIDTH, 2 * SEAT_RADIUS + 2 * SEAT_PAD);

  const seats = [];

  // Espacement horizontal
  const topStep = L / (topCount + 1);
  const bottomStep = L / (bottomCount + 1);

  // Sièges du haut
  for (let i = 0; i < topCount; i++) {
    const x = -L / 2 + topStep * (i + 1);
    const y = -W / 2 - SEAT_PAD;
    seats.push({ index: i, x, y, angle: Math.PI / 2 });
  }

  // Sièges du bas
  for (let i = 0; i < bottomCount; i++) {
    const x = -L / 2 + bottomStep * (i + 1);
    const y = +W / 2 + SEAT_PAD;
    seats.push({ index: topCount + i, x, y, angle: -Math.PI / 2 });
  }

  return { L, W, seats };
}

/* =========================
   Persistence
   ========================= */
const SAVE_KEY="plan_salle_v1";
const saveStateEl = document.getElementById("saveState");
let saveTimer=null;
function scheduleSave(){
  saveStateEl.textContent=t("saving");
  if(saveTimer) clearTimeout(saveTimer);
  saveTimer=setTimeout(()=>{
    localStorage.setItem(SAVE_KEY, JSON.stringify(state));
    saveStateEl.textContent=t("saved");
    setTimeout(()=>saveStateEl.textContent="", 1200);
  }, 350);
}
function loadState(){
  const raw = localStorage.getItem(SAVE_KEY);
  if(!raw) return;
  try {
    const s = JSON.parse(raw);
    Object.assign(state, s);
  } catch(e){ console.warn(e); }
}

/* =========================
   Rendering
   ========================= */
const svgNS="http://www.w3.org/2000/svg";
const stage = document.getElementById("stage");
const minimap = document.getElementById("minimap");
let selectedId=null;

function clear(el){ while(el.firstChild) el.removeChild(el.firstChild); }

function render(){
  clear(stage);
  const g = document.createElementNS(svgNS,"g");
  g.setAttribute("id","world");
  g.setAttribute("transform", `translate(${view.tx} ${view.ty}) scale(${view.z})`);
  stage.appendChild(g);

  // Tables
  Object.values(state.tables).forEach(tbl=>{
    const grp = document.createElementNS(svgNS,"g");
    grp.setAttribute("class","handle");
    grp.setAttribute("data-id", tbl.id);
    grp.setAttribute("transform", `translate(${tbl.x} ${tbl.y}) rotate(${tbl.rot||0})`);
    grp.addEventListener("pointerdown", tableDragStart);
    grp.addEventListener("click", (e)=>{ e.stopPropagation(); select(tbl.id); });

    if(tbl.type==="round"){
      const c = document.createElementNS(svgNS,"circle");
      c.setAttribute("r", tbl.geom.Rtable);
      c.setAttribute("class","table"+(selectedId===tbl.id?" selected":""));
      grp.appendChild(c);
    } else {
      const rect = document.createElementNS(svgNS,"rect");
      rect.setAttribute("x", -tbl.geom.L/2);
      rect.setAttribute("y", -tbl.geom.W/2);
      rect.setAttribute("width", tbl.geom.L);
      rect.setAttribute("height", tbl.geom.W);
      rect.setAttribute("rx", 14);
      rect.setAttribute("class","table"+(selectedId===tbl.id?" selected":""));
      grp.appendChild(rect);
    }

    // Seats
    tbl.seats.forEach((s, i)=>{
      const seatG = document.createElementNS(svgNS,"g");
      seatG.setAttribute("transform", `translate(${s.x} ${s.y}) rotate(${(s.angle||0)*180/Math.PI})`);

      const circ = document.createElementNS(svgNS,"circle");
      circ.setAttribute("r", 10);
      circ.setAttribute("cx", 0);
      circ.setAttribute("cy", 0);
      circ.setAttribute("class","seat");
      circ.style.fill="transparent";
      seatG.appendChild(circ);

      const occupant = s.inviteId ? state.invites[s.inviteId] : null;
      const text = document.createElementNS(svgNS,"text");
      text.setAttribute("text-anchor","middle");
      text.setAttribute("dominant-baseline","middle");
      text.setAttribute("class", occupant ? "seat-emoji" : "seat-empty");
      text.setAttribute("data-seat", i);
      text.setAttribute("data-table", tbl.id);
      if(occupant){
        text.textContent = avatarForInvite(occupant);
        text.setAttribute("draggable","true");
        text.addEventListener("dragstart", seatDragStart);
        text.addEventListener("mouseenter", (e)=>showTipSeat(e, tbl.id, i));
        text.addEventListener("mouseleave", hideTip);
      } else {
        text.textContent = "•";
      }
      seatG.addEventListener("dragover", (e)=>{ e.preventDefault(); });
      seatG.addEventListener("drop", (e)=>onDropOnSeat(e, tbl.id, i));
      seatG.addEventListener("contextmenu", (e)=>{
        e.preventDefault();
        if(tbl.seats[i].inviteId){
          const invId = tbl.seats[i].inviteId;
          unseatInvite(invId);
        }
      });

      seatG.appendChild(text);
      grp.appendChild(seatG);
    });

    // Label nom table (centré)
    const label = document.createElementNS(svgNS, "text");
label.setAttribute("text-anchor", "middle");
label.setAttribute("dominant-baseline", "middle");
label.setAttribute("y", 0); // centre vertical
label.setAttribute("class", "label-small");
label.textContent = tbl.name || tbl.id;
grp.appendChild(label);

    g.appendChild(grp);
  });

  // Objets
  Object.values(state.objects).forEach(obj=>{
    const grp = document.createElementNS(svgNS,"g");
    grp.setAttribute("transform", `translate(${obj.x} ${obj.y}) rotate(${obj.rot||0})`);
    grp.setAttribute("data-id", obj.id);
    grp.setAttribute("class","handle");
    grp.addEventListener("pointerdown", objectDragStart);
    grp.addEventListener("click", (e)=>{ e.stopPropagation(); select(obj.id); });

    const text = document.createElementNS(svgNS,"text");
    text.textContent = EMOJI_BY_OBJ[obj.type] || "❓";
    text.setAttribute("text-anchor","middle");
    text.setAttribute("dominant-baseline","middle");
    text.setAttribute("class","obj");
    grp.appendChild(text);

    const label = document.createElementNS(svgNS,"text");
    label.textContent = obj.type;
    label.setAttribute("y", 24);
    label.setAttribute("text-anchor","middle");
    label.setAttribute("class","label-small");
    grp.appendChild(label);

    g.appendChild(grp);
  });

  renderMinimap();
  renderProps();
  renderGuestList();
}

function avatarForInvite(inv){
  let e = EMOJI_BY_GENDER[inv.gender] || "🧑";
  if(inv.diets?.includes("enceinte")) e = "🤰";
  return e;
}

/* =========================
   Tables & objects creation
   ========================= */
function addRoundTable(n=8, x=-150, y=0){
  const id = uid();
  const geom = computeRoundGeometry(Math.min(12, Math.max(2, n)));
  state.tables[id] = {
    id,
    type: "round",
    x, y,
    rot: 0,
    name: `Table ${id}`,
    locked: false ,
    seats: geom.seats.map(s=>({ ...s })),
    geom: { Rseat: geom.Rseat, Rtable: geom.Rtable }
  };
  scheduleSave(); render();
  select(id);
}

function addRectTable(n=10, x=180, y=0){
  const id = uid();
  const geom = computeRectGeometry(Math.max(2, Math.min(24, n)));
  state.tables[id] = {
    id,
    type: "rect",
    x, y,
    rot: 0,
    name: `Table ${id}`,
    locked: false , 
    seats: geom.seats.map(s=>({ ...s })),
    geom: { L: geom.L, W: geom.W }
  };
  scheduleSave(); render();
  select(id);
}

function addObject(type, x=0, y=0){
  const id = uid();
  state.objects[id] = { id, type, x, y, rot: 0 };
  scheduleSave(); render();
  select(id);
}

/* =========================
   Selection & properties
   ========================= */
function select(id){
  selectedId = id;
  render();
}
function renderProps() {
  const el = document.getElementById("props");
  clear(el);

  if (!selectedId) {
    el.classList.add("muted");
    el.textContent = t("selectPrompt");
    return;
  }

  el.classList.remove("muted");

  const tbl = state.tables[selectedId];
  const obj = state.objects[selectedId];

  // === TABLE ===
  if (tbl) {
    const h = document.createElement("div");
    h.innerHTML = `<strong>${tbl.type === "round" ? t("round") : t("rect")}</strong>`;
    el.appendChild(h);

    // Bouton figer/défiger uniquement en admin
    if (document.body.getAttribute("data-mode") === "admin") {
      const lockBtn = document.createElement("button");
      lockBtn.className = "btn";
      lockBtn.textContent = tbl.locked ? "Défiger" : "Figer";
      lockBtn.addEventListener("click", () => {
        tbl.locked = !tbl.locked;
        scheduleSave();
        render();
        selectedId = tbl.id;
      });
      el.appendChild(lockBtn);
    }

    // Nom de la table
    const fName = document.createElement("div");
    fName.className = "field";
    const labN = document.createElement("label");
    labN.className = "label";
    labN.textContent = t("tableName");
    labN.setAttribute("for", "tblNameInput");
    const inpN = document.createElement("input");
    inpN.type = "text";
    inpN.id = "tblNameInput";
    inpN.className = "input";
    inpN.value = tbl.name || "";
    inpN.addEventListener("input", () => {
      tbl.name = inpN.value;
      scheduleSave();
      const tableGrp = stage.querySelector(`[data-id="${tbl.id}"]`);
      if (tableGrp) {
        const lbl = tableGrp.querySelector("text.label-small");
        if (lbl) lbl.textContent = tbl.name || tbl.id;
      }
    });
    fName.append(labN, inpN);
    el.appendChild(fName);

    // Nombre de places
    const fSeats = document.createElement("div");
    fSeats.className = "field";
    const lab = document.createElement("label");
    lab.className = "label";
    lab.textContent = t("seats");
    lab.setAttribute("for", "seatsRange");
    const inp = document.createElement("input");
    inp.type = "range";
    inp.min = "1";
    inp.max = "24";
    inp.step = "1";
    inp.value = tbl.seats.length;
    inp.id = "seatsRange";
    const out = document.createElement("div");
    out.className = "muted";
    out.textContent = tbl.seats.length;
    inp.addEventListener("input", () => {
      const count = parseInt(inp.value, 10);
      out.textContent = count;
      if (tbl.type === "round") {
        const n = Math.min(12, count);
        const g = computeRoundGeometry(n);
        tbl.geom.Rseat = g.Rseat;
        tbl.geom.Rtable = g.Rtable;
        tbl.seats = g.seats.map(s => ({ ...s }));
      } else {
        const g = computeRectGeometry(count);
        tbl.geom.L = g.L;
        tbl.geom.W = g.W;
        tbl.seats = g.seats.map(s => ({ ...s }));
      }
      scheduleSave();
      render();
      selectedId = tbl.id;
    });
    fSeats.append(lab, inp, out);
    el.appendChild(fSeats);

    // Rotation
    const fRot = document.createElement("div");
    fRot.className = "field";
    const labR = document.createElement("label");
    labR.className = "label";
    labR.textContent = t("rotate");
    labR.setAttribute("for", "rotRange");
    const inpR = document.createElement("input");
    inpR.type = "range";
    inpR.min = "-180";
    inpR.max = "180";
    inpR.value = tbl.rot || 0;
    inpR.id = "rotRange";
    const outR = document.createElement("div");
    outR.className = "muted";
    outR.textContent = (tbl.rot || 0) + "°";
    inpR.addEventListener("input", () => {
      tbl.rot = parseInt(inpR.value, 10);
      outR.textContent = tbl.rot + "°";
      scheduleSave();
      render();
      selectedId = tbl.id;
    });
    fRot.append(labR, inpR, outR);
    el.appendChild(fRot);

    // Supprimer
    const del = document.createElement("button");
    del.className = "btn";
    del.textContent = t("delete");
    del.addEventListener("click", () => {
      delete state.tables[tbl.id];
      scheduleSave();
      selectedId = null;
      render();
    });
    el.appendChild(del);
  }

  // === OBJET ===
  else if (obj) {
    const h = document.createElement("div");
    h.innerHTML = `<strong>${EMOJI_BY_OBJ[obj.type]} ${obj.type}</strong>`;
    el.appendChild(h);

    // Bouton figer/défiger uniquement en admin
    if (document.body.getAttribute("data-mode") === "admin") {
      const lockBtn = document.createElement("button");
      lockBtn.className = "btn";
      lockBtn.textContent = obj.locked ? "Défiger" : "Figer";
      lockBtn.addEventListener("click", () => {
        obj.locked = !obj.locked;
        scheduleSave();
        render();
        selectedId = obj.id;
      });
      el.appendChild(lockBtn);
    }

    // Rotation
    const fRot = document.createElement("div");
    fRot.className = "field";
    const labR = document.createElement("label");
    labR.className = "label";
    labR.textContent = t("rotate");
    labR.setAttribute("for", "objRotRange");
    const inpR = document.createElement("input");
    inpR.type = "range";
    inpR.min = "-180";
    inpR.max = "180";
    inpR.value = obj.rot || 0;
    inpR.id = "objRotRange";
    const outR = document.createElement("div");
    outR.className = "muted";
    outR.textContent = (obj.rot || 0) + "°";
    inpR.addEventListener("input", () => {
      obj.rot = parseInt(inpR.value, 10);
      outR.textContent = obj.rot + "°";
      scheduleSave();
      render();
      selectedId = obj.id;
    });
    fRot.append(labR, inpR, outR);
    el.appendChild(fRot);

    // Supprimer
    const del = document.createElement("button");
    del.className = "btn";
    del.textContent = t("delete");
    del.addEventListener("click", () => {
      delete state.objects[obj.id];
      scheduleSave();
      selectedId = null;
      render();
    });
    el.appendChild(del);
  }
}

/* =========================
   Guests
   ========================= */
function addGuest(name, gender, diets, allergies){
  const id = uid();
  state.invites[id]={ id, name, gender, diets: Array.from(diets), allergies: parseAllergies(allergies), seated:null };
  scheduleSave(); renderGuestList();
}
function parseAllergies(text){
  if(!text) return [];
  return text.split(/[;,]/).map(s=>s.trim()).filter(Boolean);
}
function renderGuestList(){
  const list = document.getElementById("guestList");
  clear(list);
  Object.values(state.invites).forEach(inv=>{
    const row = document.createElement("div");
    row.className="guest";
    row.setAttribute("draggable","true");
    row.setAttribute("data-id", inv.id);
    row.addEventListener("dragstart", guestDragStart);

    const L = document.createElement("div");
    L.className="leftx";
    const avatar = document.createElement("div");
    avatar.className="chip";
    avatar.textContent = avatarForInvite(inv);
    const name = document.createElement("div");
    name.innerHTML = `<strong>${inv.name||"(sans nom)"}</strong><div class="badges">${(inv.diets||[]).map(d=>`<span class="badge">${DIET_EMOJI[d]}</span>`).join("")}</div>`;
    L.append(avatar, name);

    const R = document.createElement("div");

    // Bouton modifier
    const editBtn = document.createElement("button");
    editBtn.className = "btn";
    editBtn.textContent = "✏ Modifier";
    editBtn.addEventListener("click", ()=>{
      document.getElementById("gName").value = inv.name || "";
      document.getElementById("gGender").value = inv.gender || "autre";
      document.querySelectorAll("#dietBoxes input").forEach(chk=>{
        chk.checked = inv.diets && inv.diets.includes(chk.value);
      });
      document.getElementById("gAllergies").value = inv.allergies ? inv.allergies.join(", ") : "";

      // On conserve l'ID pour mettre à jour au lieu d'ajouter
      document.getElementById("btnAddGuest").dataset.editingId = inv.id;
      document.getElementById("btnAddGuest").textContent = "💾 Enregistrer";
    });
    R.appendChild(editBtn);

    // Bouton supprimer
    const delBtn = document.createElement("button");
    delBtn.className = "btn danger";
    delBtn.textContent = "🗑 Supprimer";
    delBtn.addEventListener("click", ()=>{
      if(confirm(`Supprimer l'invité "${inv.name}" ?`)){
        // le retirer aussi de sa place si assis
        unseatInvite(inv.id);
        delete state.invites[inv.id];
        scheduleSave();
        renderGuestList();
        render();
      }
    });
    R.appendChild(delBtn);

    row.append(L, R);
    list.appendChild(row);
  });
}
function findSeatOfInvite(invId){
  for(const tbl of Object.values(state.tables)){
    for(const s of tbl.seats){
      if(s.inviteId===invId) return { tbl, seat:s };
    }
  }
  return null;
}
function unseatInvite(invId){
  const loc = findSeatOfInvite(invId);
  if(loc){
    delete loc.seat.inviteId;
    if(state.invites[invId]) state.invites[invId].seated=null;
    scheduleSave(); render();
  }
}

/* Drag from guest list to seat */
function guestDragStart(e){
  const id = e.currentTarget.getAttribute("data-id");
  e.dataTransfer.setData("text/plain", JSON.stringify({ kind:"guest", id }));
}
/* Drag from occupied seat to another seat */
function seatDragStart(e){
  const tbl = e.target.getAttribute("data-table");
  const seat = parseInt(e.target.getAttribute("data-seat"),10);
  const seatRef = { kind:"seat", tableId: tbl, seatIndex: seat, inviteId: state.tables[tbl].seats[seat].inviteId };
  e.dataTransfer.setData("text/plain", JSON.stringify(seatRef));
}
function onDropOnSeat(e, tableId, seatIndex){
  e.preventDefault();
  let data;
  try{ data = JSON.parse(e.dataTransfer.getData("text/plain")); } catch(err){ return; }
  const tbl = state.tables[tableId];
  const target = tbl.seats[seatIndex];
  if(data.kind==="guest"){
    const inv = state.invites[data.id];
    if(!inv) return;
    if(target.inviteId){
      const prevId = target.inviteId;
      const prevLoc = findSeatOfInvite(inv.id);
      target.inviteId = inv.id;
      inv.seated = { tableId, seatIndex };
      if(prevLoc){
        prevLoc.seat.inviteId = prevId;
        state.invites[prevId].seated = { tableId: prevLoc.tbl.id, seatIndex: prevLoc.seat.index };
      } else {
        state.invites[prevId].seated = null;
      }
    } else {
      const prev = findSeatOfInvite(inv.id);
      if(prev) prev.seat.inviteId = undefined;
      target.inviteId = inv.id;
      inv.seated = { tableId, seatIndex };
    }
  } else if(data.kind==="seat"){
    const fromTbl = state.tables[data.tableId];
    const fromSeat = fromTbl?.seats[data.seatIndex];
    if(!fromSeat || !fromSeat.inviteId) return;
    const movingId = fromSeat.inviteId;
    if(target.inviteId){
      const otherId = target.inviteId;
      target.inviteId = movingId;
      fromSeat.inviteId = otherId;
      state.invites[movingId].seated = { tableId, seatIndex };
      state.invites[otherId].seated = { tableId:data.tableId, seatIndex:data.seatIndex };
    } else {
      target.inviteId = movingId;
      fromSeat.inviteId = undefined;
      state.invites[movingId].seated = { tableId, seatIndex };
    }
  }
  scheduleSave(); render();
}

/* Tooltip */
const tip = document.getElementById("tooltip");
function showTipSeat(e, tableId, seatIndex){
  const tbl = state.tables[tableId];
  const s = tbl.seats[seatIndex];
  if(!s.inviteId) return;
  const inv = state.invites[s.inviteId];
  const diets = (inv.diets||[]).map(d=>DIET_EMOJI[d]).join(" ");
  const allerg = (inv.allergies||[]).join(", ");
  tip.textContent = `${inv.name}\n${diets}${diets?"\n":""}${allerg}`;
  tip.style.display="block";
  positionTip(e);
}
function positionTip(e){
  tip.style.left = (e.clientX + 12) + "px";
  tip.style.top  = (e.clientY + 12) + "px";
}
function hideTip(){ tip.style.display="none"; }

/* =========================
   Pan & Zoom & Minimap
   ========================= */
const view = { z:1, tx:0, ty:0 };
const zoomRange = document.getElementById("zoom");
document.getElementById("zIn").addEventListener("click", ()=> setZoom(view.z+0.1));
document.getElementById("zOut").addEventListener("click", ()=> setZoom(view.z-0.1));
zoomRange.addEventListener("input", ()=> setZoom(parseFloat(zoomRange.value)));
function setZoom(z){
  view.z = Math.min(3, Math.max(0.5, z));
  zoomRange.value = view.z;
  render();
}
let panning=false, panStart=null;
window.addEventListener("keydown", (e)=>{ if(e.code==="Space") panning=true; });
window.addEventListener("keyup", (e)=>{ if(e.code==="Space") panning=false; });
stage.addEventListener("pointerdown", (e)=>{
  if(panning){ panStart={ x:e.clientX, y:e.clientY, tx:view.tx, ty:view.ty }; stage.setPointerCapture(e.pointerId); }
  hideTip();
});
stage.addEventListener("pointermove", (e)=>{
  if(panStart){
    const dx = (e.clientX - panStart.x);
    const dy = (e.clientY - panStart.y);
    view.tx = panStart.tx + dx/view.z;
    view.ty = panStart.ty + dy/view.z;
    render();
  }
});
stage.addEventListener("pointerup", (e)=>{ if(panStart){ stage.releasePointerCapture(e.pointerId); panStart=null; } });
stage.addEventListener("wheel", (e)=>{
  if(e.ctrlKey) return;
  e.preventDefault();
  const delta = e.deltaY<0 ? 1.05 : 0.95;
  setZoom(view.z*delta);
});

function renderMinimap(){
  clear(minimap);
  const vb = stage.getAttribute("viewBox").split(" ").map(parseFloat);
  const [vx, vy, vw, vh] = vb;
  const mw = minimap.clientWidth || 180, mh = minimap.clientHeight || 120;
  const scale = Math.min(mw/vw, mh/vh);
  const g = document.createElementNS(svgNS,"g");
  g.setAttribute("transform", `translate(${mw/2} ${mh/2}) scale(${scale})`);
  minimap.appendChild(g);

  const bg = document.createElementNS(svgNS,"rect");
  bg.setAttribute("x",0); bg.setAttribute("y",0); bg.setAttribute("width", mw); bg.setAttribute("height", mh);
  bg.setAttribute("fill","transparent");
  minimap.appendChild(bg);

  Object.values(state.tables).forEach(tbl=>{
    if(tbl.type==="round"){
      const c = document.createElementNS(svgNS,"circle");
      c.setAttribute("cx", tbl.x + view.tx);
      c.setAttribute("cy", tbl.y + view.ty);
      c.setAttribute("r", tbl.geom.Rtable);
      c.setAttribute("fill","#e2e8f0");
      c.setAttribute("stroke","#cbd5e1");
      g.appendChild(c);
    } else {
      const r = document.createElementNS(svgNS,"rect");
      r.setAttribute("x", tbl.x - tbl.geom.L/2 + view.tx);
      r.setAttribute("y", tbl.y - tbl.geom.W/2 + view.ty);
      r.setAttribute("width", tbl.geom.L);
      r.setAttribute("height", tbl.geom.W);
      r.setAttribute("fill","#e2e8f0");
      r.setAttribute("stroke","#cbd5e1");
      g.appendChild(r);
    }
  });

  Object.values(state.objects).forEach(obj=>{
    const o = document.createElementNS(svgNS,"circle");
    o.setAttribute("cx", obj.x + view.tx);
    o.setAttribute("cy", obj.y + view.ty);
    o.setAttribute("r", 8);
    o.setAttribute("fill","#94a3b8");
    g.appendChild(o);
  });

  const vRect = document.createElementNS(svgNS,"rect");
  vRect.setAttribute("x", (mw/2 + (0 - view.tx)*scale) );
  vRect.setAttribute("y", (mh/2 + (0 - view.ty)*scale) );
  vRect.setAttribute("width", (vw/ view.z)*scale);
  vRect.setAttribute("height", (vh/ view.z)*scale);
  vRect.setAttribute("fill","rgba(59,130,246,.12)");
  vRect.setAttribute("stroke","#3b82f6");
  vRect.setAttribute("stroke-width","1");
  minimap.appendChild(vRect);

  minimap.onclick = (e)=>{
    const rect = minimap.getBoundingClientRect();
    const cx = (e.clientX - rect.left) - mw/2;
    const cy = (e.clientY - rect.top) - mh/2;
    view.tx = -cx/scale;
    view.ty = -cy/scale;
    render();
  };
}

/* =========================
   Dragging tables/objects
   ========================= */
let dragCtx=null;
function tableDragStart(e){
  const id = e.currentTarget.getAttribute("data-id");
  if (state.tables[id]?.locked) return; // ← empêche le drag si figée
  select(id);
  const pt = toWorld(e);
  dragCtx = { kind:"table", id, ox: state.tables[id].x - pt.x, oy: state.tables[id].y - pt.y };
  window.addEventListener("pointermove", onDragMove);
  window.addEventListener("pointerup", onDragEnd, { once:true });
}
function objectDragStart(e){
  const id = e.currentTarget.getAttribute("data-id");
  select(id);
  const pt = toWorld(e);
  dragCtx = { kind:"object", id, ox: state.objects[id].x - pt.x, oy: state.objects[id].y - pt.y };
  window.addEventListener("pointermove", onDragMove);
  window.addEventListener("pointerup", onDragEnd, { once:true });
}
function toWorld(e){
  const rect = stage.getBoundingClientRect();
  const sx = (e.clientX - rect.left) / rect.width;
  const sy = (e.clientY - rect.top) / rect.height;
  const vb = stage.getAttribute("viewBox").split(" ").map(parseFloat);
  const [vx, vy, vw, vh] = vb;
  const x = vx + sx*vw;
  const y = vy + sy*vh;
  return { x: (x - view.tx)/view.z, y:(y - view.ty)/view.z };
}
function onDragMove(e){
  if(!dragCtx) return;
  const pt = toWorld(e);
  if(dragCtx.kind==="table"){
    const tbl = state.tables[dragCtx.id];
    tbl.x = Math.round(pt.x + dragCtx.ox);
    tbl.y = Math.round(pt.y + dragCtx.oy);
  } else if(dragCtx.kind==="object"){
    const obj = state.objects[dragCtx.id];
    obj.x = Math.round(pt.x + dragCtx.ox);
    obj.y = Math.round(pt.y + dragCtx.oy);
  }
  render();
}
function onDragEnd(){
  window.removeEventListener("pointermove", onDragMove);
  dragCtx=null; scheduleSave();
}

/* =========================
   UI events
   ========================= */
document.getElementById("addRound").addEventListener("click", ()=>addRoundTable(8, -150, 0));
document.getElementById("addRect").addEventListener("click", ()=>addRectTable(10, 200, 0));
document.querySelectorAll("[data-obj]").forEach(btn=>{
  btn.addEventListener("click", ()=> addObject(btn.getAttribute("data-obj"), 0, 0));
});

document.getElementById("btnAddGuest").addEventListener("click", ()=>{
  const name = document.getElementById("gName").value.trim();
  const gender = document.getElementById("gGender").value;
  const diets = Array.from(document.querySelectorAll("#dietBoxes input:checked")).map(i=>i.value);
  const allergies = document.getElementById("gAllergies").value.trim();
  if(!name){ alert("Nom requis"); return; }

  const editingId = document.getElementById("btnAddGuest").dataset.editingId;
  if(editingId){
    // Mise à jour d'un invité existant
    state.invites[editingId].name = name;
    state.invites[editingId].gender = gender;
    state.invites[editingId].diets = diets;
    state.invites[editingId].allergies = parseAllergies(allergies);
    delete document.getElementById("btnAddGuest").dataset.editingId;
    document.getElementById("btnAddGuest").textContent = "+ Ajouter";
  } else {
    // Ajout normal
    addGuest(name, gender, diets, allergies);
  }

  // Réinitialiser le formulaire
  document.getElementById("gName").value="";
  document.querySelectorAll("#dietBoxes input:checked").forEach(i=>i.checked=false);
  document.getElementById("gAllergies").value="";
  scheduleSave();
  renderGuestList();
  render();
});
document.getElementById("btnExport").addEventListener("click", ()=>{
  const blob = new Blob([JSON.stringify(state, null, 2)], { type:"application/json" });
  const a = document.createElement("a");
  a.href = URL.createObjectURL(blob);
  a.download = "plan_salle.json";
  document.body.appendChild(a);
  a.click();
  a.remove();
});
document.getElementById("fileImport").addEventListener("change", (e)=>{
  const file = e.target.files[0]; if(!file) return;
  const fr = new FileReader();
  fr.onload = ()=>{
    try{
      const data = JSON.parse(fr.result);
      if(!data || !data.version) throw new Error("Fichier invalide");
      Object.assign(state, data);
      render();
      renderGuestList();
      scheduleSave();
    } catch(err){ alert("Import invalide"); }
  };
  fr.readAsText(file);
});
document.getElementById("btnPrint").addEventListener("click", ()=> window.print());

stage.addEventListener("click", ()=>{ selectedId=null; render(); });

document.getElementById("langSel").addEventListener("change", (e)=>{
  LANG = e.target.value || "fr";
  localStorage.setItem("lang", LANG);
  applyI18N(); renderGuestList(); renderProps();
});

/* =========================
   Init
   ========================= */
(function init(){
  LANG = localStorage.getItem("lang") || "fr";
  document.getElementById("langSel").value = LANG;
  applyI18N();
  loadState();
  if(Object.keys(state.tables).length===0 && Object.keys(state.objects).length===0){
    addRoundTable(8, -180, -40);
    addRectTable(10, 240, 40);
    addObject("buffet", -320, -180);
    addObject("bar", 360, -180);
  } else {
    render();
  }
  window.addEventListener("mousemove", (e)=>{ if(tip.style.display==="block") positionTip(e); });
})();

function generateListing(){
  let html = `<h1 style="font-size:18px;">Listing du plan de salle</h1>`;
  Object.values(state.tables).forEach(tbl=>{
    const guests = tbl.seats.map(s => s.inviteId ? state.invites[s.inviteId] : null).filter(Boolean);
    const total = guests.length;

    let adultes = 0, enfants = 0;
    guests.forEach(g=>{
      if(["homme","femme"].includes(g.gender)) adultes++;
      else enfants++;
    });

    const regimes = {};
    guests.forEach(g=>{
      (g.diets||[]).forEach(d=> regimes[d] = (regimes[d]||0)+1 );
    });

    const allergies = guests.filter(g=>g.allergies?.length).map(g=>`${g.name}: ${g.allergies.join(", ")}`);

    html += `<h2 style="margin-top:16px;">${tbl.name || ("Table "+tbl.id)}</h2>`;
html += `<p>${total} couvert(s) — ${adultes} adulte(s), ${enfants} enfant(s)</p>`;

if(guests.length){
  html += `<ul>`;
  guests.forEach(g => {
    const regimes = (g.diets||[]).map(d => `${DIET_EMOJI[d] || ""} ${d}`).join(", ");
    const allerg = (g.allergies||[]).join(", ");
    let line = `<strong>${g.name}</strong>`;
    if(regimes) line += ` — Régime: ${regimes}`;
    if(allerg) line += ` — Allergie: ${allerg}`;
    html += `<li>${line}</li>`;
  });
  html += `</ul>`;
    }
  });

  const win = window.open("", "_blank");
  win.document.write(`<html><head><title>Listing</title><style>body{font-family:sans-serif;padding:20px;} h1,h2{margin-bottom:6px;} p{margin:4px 0;} ul{margin:0;padding-left:20px;}</style></head><body>${html}</body></html>`);
  win.document.close();
    win.print();
}

document.getElementById("btnListing").addEventListener("click", generateListing);
</script>

<div id="adminModal" style="display:none; position:fixed; z-index:1000; left:0; top:0; width:100%; height:100%; background-color:rgba(0,0,0,0.5);">
<div style="background:#fff; margin:15% auto; padding:20px; width:300px; border-radius:10px; text-align:center;">
<h3>Mot de passe admin</h3>
<input id="adminPassword" placeholder="Mot de passe" style="width:100%; padding:8px; margin:10px 0;" type="password"/>
<button class="btn" onclick="validateAdminPassword()">Valider</button>
<button class="btn" onclick="document.getElementById('adminModal').style.display='none'">Annuler</button>
<hr style="margin: 10px 0;"/>
<h4>Changer le mot de passe</h4>
<input id="newPassword" placeholder="Nouveau mot de passe" style="width:100%; padding:8px; margin:10px 0;" type="password"/>
<button class="btn" onclick="changeAdminPassword()">Changer</button>
</div>
</div>
<script>
    let adminPassword = localStorage.getItem("adminPassword") || "admin123";
    function validateAdminPassword() {
      const pwd = document.getElementById("adminPassword").value;
      if (pwd === adminPassword) {
        alert("Mode administrateur activé");
        document.body.setAttribute("data-mode", "admin");
        document.getElementById("adminModal").style.display = "none";
        document.getElementById("adminControls").style.display = "inline-block";
      } else {
        alert("Mot de passe incorrect");
      }
    }
    function changeAdminPassword() {
      const newPwd = document.getElementById("newPassword").value;
      if (newPwd.length < 4) {
        alert("Le mot de passe doit contenir au moins 4 caractères.");
        return;
      }
      localStorage.setItem("adminPassword", newPwd);
      adminPassword = newPwd;
      alert("Mot de passe mis à jour.");
      document.getElementById("newPassword").value = "";
    }
    function lockObject(id) {
  if (state.objects[id]) {
    state.objects[id].locked = true;
    scheduleSave(); render();
  }
}
function unlockObject(id) {
  if (state.objects[id]) {
    state.objects[id].locked = false;
    scheduleSave(); render();
  }
}
    const originalObjectDragStart = objectDragStart;
objectDragStart = function(e) {
  const id = e.currentTarget.getAttribute("data-id");
  if (state.objects[id]?.locked) return;
  originalObjectDragStart(e);
    };
    </script>
</body>
</html>
