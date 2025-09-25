<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>DSPC · Gestión de Flota (v2)</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { background:#f4f6f8; }
    header {
      height: 260px;
      background: center/cover no-repeat url('ram700_hd.jpg');
      display:flex; align-items:center; justify-content:center;
      color:#fff; text-shadow:0 3px 12px rgba(0,0,0,.45);
      border-bottom-left-radius:18px; border-bottom-right-radius:18px;
      position: relative;
    }
    header h1 { background:rgba(0,0,0,0.45); padding:10px 18px; border-radius:10px; margin:0; }
    /* status en header (arriba-derecha) */
    .header-status { position:absolute; top:12px; right:16px; display:flex; gap:8px; align-items:center; }
    .nav-tabs .nav-link.active{ background:#800040;color:#fff;border-color:#800040;}
    .card{ border-radius:12px; }
    .thumb{ width:70px;height:50px;object-fit:cover;border-radius:6px;border:1px solid #ddd }
    .small-muted{ font-size:.9rem;color:#6b6f76 }
  </style>
</head>
<body>
  <header>
    <h1>DSPC · Gestión Integral de Flota — RAM 700 (2025)</h1>
    <div class="header-status">
      <span id="supabaseStatus" class="badge bg-secondary">Conexión: comprobando...</span>
      <button id="btnTestSupabase" class="btn btn-sm btn-outline-light">Probar conexión</button>
    </div>
  </header>

  <main class="container my-4">
    <!-- Notificación breve (se muestra al cargar conexión) -->
    <div id="connNoticeArea"></div>

    <ul class="nav nav-tabs mb-2" id="mainTabs" role="tablist">
      <li class="nav-item"><button class="nav-link active" data-bs-toggle="tab" data-bs-target="#vehiculos">Vehículos</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#mantenciones">Mantenciones</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#limpiezas">Limpiezas</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#siniestros">Siniestros</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#conductores">Conductores</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#conductorDiario">Conductor Diario</button></li>
    </ul>

    <div class="tab-content bg-white border rounded shadow-sm p-3">
      <!-- VEHÍCULOS -->
      <div class="tab-pane fade show active" id="vehiculos">
        <div class="row">
          <div class="col-lg-4">
            <form id="formVehiculo" class="card card-body">
              <h5>Registrar Vehículo</h5>
              <label class="form-label required">Patente</label>
              <input id="patente" class="form-control mb-2" required>
              <label class="form-label required">Marca</label>
              <input id="marca" class="form-control mb-2" required>
              <label class="form-label required">Modelo</label>
              <input id="modelo" class="form-control mb-2" required>
              <label class="form-label required">Año</label>
              <input id="anio" type="number" class="form-control mb-2" min="1900" max="2100">
              <label class="form-label">Kilometraje</label>
              <input id="km" type="number" class="form-control mb-2">
              <label class="form-label">Responsable</label>
              <input id="responsable" class="form-control mb-3">
              <button class="btn btn-primary w-100">Guardar vehículo</button>
            </form>
          </div>

          <div class="col-lg-8">
            <div class="d-flex justify-content-between align-items-center mb-2">
              <h6 class="mb-0">Listado de Vehículos</h6>
              <div class="small-muted">Actualizado desde Supabase</div>
            </div>
            <div class="table-responsive" style="max-height:420px;overflow:auto">
              <table class="table table-sm table-striped">
                <thead class="table-light">
                  <tr><th>ID</th><th>Patente</th><th>Marca</th><th>Modelo</th><th>Año</th><th>KM</th><th>Responsable</th><th>Registro</th></tr>
                </thead>
                <tbody id="tbodyVehiculos"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>

      <!-- MANTENCIONES -->
      <div class="tab-pane fade" id="mantenciones">
        <div class="row">
          <div class="col-lg-5">
            <form id="formMantencion" class="card card-body">
              <h5>Registrar Mantención</h5>
              <label>Vehículo</label>
              <select id="mant_vehiculo_id" class="form-select mb-2" required></select>
              <label>Fecha</label>
              <input id="mant_fecha" type="date" class="form-control mb-2" required>
              <label>Tipo</label>
              <select id="mant_tipo" class="form-select mb-2" required>
                <option>Cambio de aceite</option>
                <option>Frenos</option>
                <option>Neumáticos</option>
                <option>Batería</option>
                <option>Alineación y balanceo</option>
                <option>Revisión general</option>
              </select>
              <label>Kilometraje</label>
              <input id="mant_km" type="number" class="form-control mb-2" required>
              <label>Costo</label>
              <input id="mant_costo" type="number" class="form-control mb-2">
              <label>Observación</label>
              <textarea id="mant_obs" class="form-control mb-3"></textarea>
              <button class="btn btn-success w-100">Guardar mantención</button>
            </form>
          </div>
          <div class="col-lg-7">
            <h6>Tabla de Mantenciones</h6>
            <div class="table-responsive" style="max-height:240px;overflow:auto">
              <table class="table table-striped table-sm">
                <thead class="table-light"><tr><th>ID</th><th>Vehículo</th><th>Fecha</th><th>Tipo</th><th>Km</th><th>Costo</th><th>Obs</th></tr></thead>
                <tbody id="tbodyMantenciones"></tbody>
              </table>
            </div>

            <div class="card p-3 mt-3">
              <h6 class="mb-2">Gráfica: Mantenciones por Vehículo</h6>
              <canvas id="chartMantenciones" height="100"></canvas>
            </div>
          </div>
        </div>
      </div>

      <!-- LIMPIEZAS -->
      <div class="tab-pane fade" id="limpiezas">
        <div class="row">
          <div class="col-lg-5">
            <form id="formLimpieza" class="card card-body">
              <h5>Registrar Limpieza</h5>
              <label>Vehículo</label>
              <select id="limp_vehiculo_id" class="form-select mb-2" required></select>
              <label>Fecha</label>
              <input id="limp_fecha" type="date" class="form-control mb-2" required>
              <label>Observación</label>
              <textarea id="limp_obs" class="form-control mb-3"></textarea>
              <button class="btn btn-info w-100">Guardar limpieza</button>
            </form>
          </div>
          <div class="col-lg-7">
            <h6>Tabla de Limpiezas</h6>
            <div class="table-responsive" style="max-height:420px;overflow:auto">
              <table class="table table-sm table-striped">
                <thead class="table-light"><tr><th>ID</th><th>Vehículo</th><th>Fecha</th><th>Obs</th></tr></thead>
                <tbody id="tbodyLimpiezas"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>

      <!-- SINIESTROS -->
      <div class="tab-pane fade" id="siniestros">
        <div class="row">
          <div class="col-lg-5">
            <form id="formSiniestro" class="card card-body">
              <h5>Registrar Siniestro</h5>
              <label>Vehículo</label>
              <select id="sini_vehiculo_id" class="form-select mb-2" required></select>
              <label>Fecha</label>
              <input id="sini_fecha" type="date" class="form-control mb-2" required>
              <label>Descripción</label>
              <textarea id="sini_desc" class="form-control mb-2" required></textarea>
              <label>Costo</label>
              <input id="sini_costo" type="number" class="form-control mb-2">
              <label>Fotos (máx 3)</label>
              <input id="sini_files" type="file" class="form-control mb-3" accept="image/*" multiple>
              <button class="btn btn-warning w-100">Guardar siniestro</button>
            </form>
          </div>
          <div class="col-lg-7">
            <h6>Tabla de Siniestros</h6>
            <div class="table-responsive" style="max-height:420px;overflow:auto">
              <table class="table table-sm table-striped">
                <thead class="table-light"><tr><th>ID</th><th>Vehículo</th><th>Fecha</th><th>Desc</th><th>Fotos</th></tr></thead>
                <tbody id="tbodySiniestros"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>

      <!-- CONDUCTORES -->
      <div class="tab-pane fade" id="conductores">
        <div class="row">
          <div class="col-lg-5">
            <form id="formConductor" class="card card-body">
              <h5>Registrar Conductor</h5>
              <label>Nombre</label>
              <input id="cond_nombre" class="form-control mb-2" required>
              <label>Apellido</label>
              <input id="cond_apellido" class="form-control mb-2">
              <label>RUT</label>
              <input id="cond_rut" class="form-control mb-2">
              <label>Licencia</label>
              <input id="cond_licencia" class="form-control mb-3">
              <button class="btn btn-secondary w-100">Guardar conductor</button>
            </form>
          </div>
          <div class="col-lg-7">
            <h6>Tabla de Conductores</h6>
            <div class="table-responsive" style="max-height:420px;overflow:auto">
              <table class="table table-sm table-striped">
                <thead class="table-light"><tr><th>ID</th><th>Nombre</th><th>Apellido</th><th>RUT</th><th>Licencia</th></tr></thead>
                <tbody id="tbodyConductores"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>

      <!-- CONDUCTOR DIARIO -->
      <div class="tab-pane fade" id="conductorDiario">
        <div class="row">
          <div class="col-lg-5">
            <form id="formConductorDiario" class="card card-body">
              <h5>Asignar Conductor Diario</h5>
              <label>Vehículo</label>
              <select id="cd_vehiculo_id" class="form-select mb-2" required></select>
              <label>Conductor</label>
              <select id="cd_conductor_id" class="form-select mb-2" required></select>
              <label>Fecha</label>
              <input id="cd_fecha" type="date" class="form-control mb-2" required>
              <label>Km inicio</label>
              <input id="cd_km_inicio" type="number" class="form-control mb-2">
              <label>Km fin</label>
              <input id="cd_km_fin" type="number" class="form-control mb-2">
              <label>Fotos (máx 3)</label>
              <input id="cd_files" type="file" class="form-control mb-3" accept="image/*" multiple>
              <button class="btn btn-dark w-100">Guardar conductor diario</button>
            </form>
          </div>
          <div class="col-lg-7">
            <h6>Tabla Conductor Diario</h6>
            <div class="table-responsive" style="max-height:420px;overflow:auto">
              <table class="table table-sm table-striped">
                <thead class="table-light"><tr><th>ID</th><th>Vehículo</th><th>Conductor</th><th>Fecha</th><th>Fotos</th></tr></thead>
                <tbody id="tbodyConductorDiario"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>

    </div>
  </main>

  <!-- scripts -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4"></script>
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

  <script>
  (function(){
    // --------- CONFIG (ya con tu clave) ----------
    const SUPABASE_URL = "https://qnufcrywbncbeosfmwgc.supabase.co";
    const SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InFudWZjcnl3Ym5jYmVvc2Ztd2djIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NTczNzU4MDEsImV4cCI6MjA3Mjk1MTgwMX0.Ag37eDgN-88h1uvTln9V40CPKSkZvyBF5j-u0EM46mM";
    const supabase = window.supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

    // --------- UI refs ----------
    const statusEl = document.getElementById('supabaseStatus');
    const btnTest = document.getElementById('btnTestSupabase');
    const connNoticeArea = document.getElementById('connNoticeArea');

    const tbodyVehiculos = document.getElementById('tbodyVehiculos');
    const tbodyMantenciones = document.getElementById('tbodyMantenciones');
    const tbodyLimpiezas = document.getElementById('tbodyLimpiezas');
    const tbodySiniestros = document.getElementById('tbodySiniestros');
    const tbodyConductores = document.getElementById('tbodyConductores');
    const tbodyConductorDiario = document.getElementById('tbodyConductorDiario');

    // helpers
    function escapeHtml(s){ if(s===null||s===undefined) return ''; return String(s).replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;'); }
    function setStatus(ok, text){
      if(ok){ statusEl.textContent = text || "Conexión ✓"; statusEl.className = "badge bg-success"; }
      else { statusEl.textContent = text || "Conexión ✕"; statusEl.className = "badge bg-danger"; }
    }
    function showConnNotice(ok, text){
      connNoticeArea.innerHTML = `<div class="alert ${ok? 'alert-success':'alert-danger'} alert-sm" role="alert">${escapeHtml(text)}</div>`;
      setTimeout(()=>{ connNoticeArea.innerHTML=''; }, 4500);
    }

    // Test conexión
    async function testConnection(){
      try {
        const { data, error } = await supabase.from('vehiculos').select('id').limit(1);
        if(error){ console.error(error); setStatus(false); showConnNotice(false, 'Sin conexión a Supabase'); return false; }
        setStatus(true); showConnNotice(true, 'Conectado a Supabase ✅'); return true;
      } catch(e){ console.error(e); setStatus(false); showConnNotice(false, 'Sin conexión a Supabase'); return false; }
    }
    btnTest?.addEventListener('click', testConnection);

    // Upload helper (bucket 'fotos' - crear bucket público en Supabase)
    async function uploadFiles(files, prefix){
      if(!files || files.length===0) return [];
      const urls = [];
      for(let i=0;i<Math.min(files.length,3);i++){
        const file = files[i];
        const filename = `${prefix}/${crypto.randomUUID()}_${file.name.replace(/\s/g,'_')}`;
        const { error } = await supabase.storage.from('fotos').upload(filename, file, { cacheControl: '3600', upsert: false });
        if(error){ console.error('upload error', error); continue; }
        const publicUrl = supabase.storage.from('fotos').getPublicUrl(filename).publicUrl;
        urls.push(publicUrl);
      }
      return urls;
    }

    // Fill selects
    async function fillVehiculoSelects(){
      const { data } = await supabase.from('vehiculos').select('id,patente,marca,modelo').order('id',{ascending:true});
      const vehList = data || [];
      const ids = ['mant_vehiculo_id','limp_vehiculo_id','sini_vehiculo_id','cd_vehiculo_id'];
      ids.forEach(id=>{
        const sel = document.getElementById(id);
        if(!sel) return;
        sel.innerHTML = '<option value="">Seleccione vehículo</option>';
        vehList.forEach(v => sel.insertAdjacentHTML('beforeend', `<option value="${v.id}">${escapeHtml(v.patente)} - ${escapeHtml(v.marca)} ${escapeHtml(v.modelo)}</option>`));
      });
      // conductor diario conductor select
      const conds = await supabase.from('conductores').select('id,nombre,apellido').order('id',{ascending:true});
      const cdSel = document.getElementById('cd_conductor_id');
      if(cdSel){
        cdSel.innerHTML = '<option value="">Seleccione conductor</option>';
        (conds.data || []).forEach(c => cdSel.insertAdjacentHTML('beforeend', `<option value="${c.id}">${escapeHtml(c.nombre)} ${escapeHtml(c.apellido||'')}</option>`));
      }
    }

    // Render tablas
    async function renderVehiculos(){
      const { data, error } = await supabase.from('vehiculos').select('id,patente,marca,modelo,anio,km,responsable,fecha_registro').order('id',{ascending:true});
      if(error){ console.error(error); return; }
      tbodyVehiculos.innerHTML = '';
      (data||[]).forEach(v => {
        tbodyVehiculos.insertAdjacentHTML('beforeend', `<tr>
          <td>${v.id}</td>
          <td>${escapeHtml(v.patente)}</td>
          <td>${escapeHtml(v.marca)}</td>
          <td>${escapeHtml(v.modelo)}</td>
          <td>${v.anio||''}</td>
          <td>${v.km||''}</td>
          <td>${escapeHtml(v.responsable)||''}</td>
          <td>${v.fecha_registro||''}</td>
        </tr>`);
      });
    }

    async function renderMantenciones(){
      const { data, error } = await supabase.from('mantenciones').select('id,vehiculo_id,fecha,tipo,kilometraje,costo,observacion').order('fecha',{ascending:false});
      if(error){ console.error(error); return; }
      const veh = (await supabase.from('vehiculos').select('id,patente')).data || [];
      const vehMap = {}; veh.forEach(v=> vehMap[v.id] = v.patente);
      tbodyMantenciones.innerHTML = '';
      (data||[]).forEach(m=>{
        tbodyMantenciones.insertAdjacentHTML('beforeend', `<tr>
          <td>${m.id}</td><td>${escapeHtml(vehMap[m.vehiculo_id]||'')}</td><td>${m.fecha||''}</td><td>${escapeHtml(m.tipo||'')}</td><td>${m.kilometraje||''}</td><td>${m.costo||''}</td><td>${escapeHtml(m.observacion||'')}</td>
        </tr>`);
      });
    }

    async function renderLimpiezas(){
      const { data, error } = await supabase.from('limpiezas').select('*').order('fecha',{ascending:false});
      if(error){ console.error(error); return; }
      const veh = (await supabase.from('vehiculos').select('id,patente')).data || [];
      const vehMap = {}; veh.forEach(v=> vehMap[v.id] = v.patente);
      tbodyLimpiezas.innerHTML = '';
      (data||[]).forEach(r=>{
        tbodyLimpiezas.insertAdjacentHTML('beforeend', `<tr>
          <td>${r.id}</td><td>${escapeHtml(vehMap[r.vehiculo_id]||'')}</td><td>${r.fecha||''}</td><td>${escapeHtml(r.observacion||'')}</td>
        </tr>`);
      });
    }

    async function renderSiniestros(){
      const { data, error } = await supabase.from('siniestros').select('*').order('fecha',{ascending:false});
      if(error){ console.error(error); return; }
      const veh = (await supabase.from('vehiculos').select('id,patente')).data || [];
      const vehMap = {}; veh.forEach(v=> vehMap[v.id] = v.patente);
      tbodySiniestros.innerHTML = '';
      (data||[]).forEach(s=>{
        const fotos = (s.fotos||[]).map(u=> `<a href="${u}" target="_blank"><img src="${u}" class="thumb me-1"></a>`).join('');
        tbodySiniestros.insertAdjacentHTML('beforeend', `<tr>
          <td>${s.id}</td><td>${escapeHtml(vehMap[s.vehiculo_id]||'')}</td><td>${s.fecha||''}</td><td>${escapeHtml(s.descripcion||'')}</td><td>${fotos}</td>
        </tr>`);
      });
    }

    async function renderConductores(){
      const { data, error } = await supabase.from('conductores').select('*').order('id',{ascending:true});
      if(error){ console.error(error); return; }
      tbodyConductores.innerHTML = '';
      (data||[]).forEach(c=>{
        tbodyConductores.insertAdjacentHTML('beforeend', `<tr>
          <td>${c.id}</td><td>${escapeHtml(c.nombre)}</td><td>${escapeHtml(c.apellido||'')}</td><td>${escapeHtml(c.rut||'')}</td><td>${escapeHtml(c.licencia||'')}</td>
        </tr>`);
      });
    }

    async function renderConductorDiario(){
      const { data, error } = await supabase.from('conductor_diario').select('*').order('fecha',{ascending:false});
      if(error){ console.error(error); return; }
      const veh = (await supabase.from('vehiculos').select('id,patente')).data || [];
      const cond = (await supabase.from('conductores').select('id,nombre,apellido')).data || [];
      const vehMap = {}; veh.forEach(v=> vehMap[v.id] = v.patente);
      const condMap = {}; cond.forEach(c=> condMap[c.id] = `${c.nombre} ${c.apellido||''}`);
      tbodyConductorDiario.innerHTML = '';
      (data||[]).forEach(d=>{
        const fotos = (d.fotos||[]).map(u=> `<a href="${u}" target="_blank"><img src="${u}" class="thumb me-1"></a>`).join('');
        tbodyConductorDiario.insertAdjacentHTML('beforeend', `<tr>
          <td>${d.id}</td><td>${escapeHtml(vehMap[d.vehiculo_id]||'')}</td><td>${escapeHtml(condMap[d.conductor_id]||'')}</td><td>${d.fecha||''}</td><td>${fotos}</td>
        </tr>`);
      });
    }

    // Chart mantenciones
    let chartMant;
    async function renderChartMantenciones(){
      const { data } = await supabase.from('mantenciones').select('vehiculo_id');
      const veh = (await supabase.from('vehiculos').select('id,patente')).data || [];
      const vehMap = {}; veh.forEach(v=> vehMap[v.id] = v.patente);
      const counts = {};
      (data||[]).forEach(m => counts[m.vehiculo_id] = (counts[m.vehiculo_id]||0)+1);
      const labels = Object.keys(vehMap).map(k => vehMap[k]);
      const dataset = Object.keys(vehMap).map(k => counts[k]||0);
      const ctx = document.getElementById('chartMantenciones')?.getContext('2d');
      if(!ctx) return;
      if(chartMant) chartMant.destroy();
      chartMant = new Chart(ctx, {
        type: 'bar',
        data: { labels, datasets:[{ label: 'Mantenciones', data: dataset, backgroundColor: '#800040' }]},
        options: { responsive:true, scales:{ y:{ beginAtZero:true, ticks:{ precision:0 } } } }
      });
    }

    // Cargar todo
    async function cargarTodo(){
      await fillVehiculoSelects();
      await renderVehiculos();
      await renderMantenciones();
      await renderLimpiezas();
      await renderSiniestros();
      await renderConductores();
      await renderConductorDiario();
      await renderChartMantenciones();
    }

    // Form handlers (verifican conexión antes de insertar)
    async function safeInsert(tabla, datos){
      const ok = await testConnection();
      if(!ok) { alert('No hay conexión a Supabase. Revisa la API Key o la URL.'); return null; }
      const { data, error } = await supabase.from(tabla).insert([datos]);
      if(error){ alert('❌ Error: '+ error.message); console.error(error); return null; }
      return data;
    }

    // Vehículos
    document.getElementById('formVehiculo')?.addEventListener('submit', async (e)=>{
      e.preventDefault();
      const d = {
        patente: document.getElementById('patente').value.trim(),
        marca: document.getElementById('marca').value.trim(),
        modelo: document.getElementById('modelo').value.trim(),
        anio: parseInt(document.getElementById('anio').value) || null,
        km: parseInt(document.getElementById('km').value) || null,
        responsable: document.getElementById('responsable').value.trim() || null
      };
      const res = await safeInsert('vehiculos', d);
      if(res) { alert('✅ Vehículo guardado'); e.target.reset(); await cargarTodo(); }
    });

    // Mantenciones
    document.getElementById('formMantencion')?.addEventListener('submit', async (e)=>{
      e.preventDefault();
      const d = {
        vehiculo_id: parseInt(document.getElementById('mant_vehiculo_id').value),
        fecha: document.getElementById('mant_fecha').value,
        tipo: document.getElementById('mant_tipo').value,
        kilometraje: parseInt(document.getElementById('mant_km').value) || null,
        costo: parseFloat(document.getElementById('mant_costo').value) || null,
        observacion: document.getElementById('mant_obs').value || null
      };
      const res = await safeInsert('mantenciones', d);
      if(res){ alert('✅ Mantención guardada'); e.target.reset(); await cargarMantenciones(); await renderChartMantenciones(); }
    });

    // Limpiezas
    document.getElementById('formLimpieza')?.addEventListener('submit', async (e)=>{
      e.preventDefault();
      const d = {
        vehiculo_id: parseInt(document.getElementById('limp_vehiculo_id').value),
        fecha: document.getElementById('limp_fecha').value,
        observacion: document.getElementById('limp_obs').value || null
      };
      const res = await safeInsert('limpiezas', d);
      if(res){ alert('✅ Limpieza guardada'); e.target.reset(); await cargarLimpiezas(); }
    });

    // Siniestros (sube fotos)
    document.getElementById('formSiniestro')?.addEventListener('submit', async (e)=>{
      e.preventDefault();
      const files = document.getElementById('sini_files').files;
      const fotos = await uploadFiles(files, 'siniestros');
      const d = {
        vehiculo_id: parseInt(document.getElementById('sini_vehiculo_id').value),
        fecha: document.getElementById('sini_fecha').value,
        descripcion: document.getElementById('sini_desc').value,
        costo: parseFloat(document.getElementById('sini_costo').value) || null,
        fotos: fotos
      };
      const res = await safeInsert('siniestros', d);
      if(res){ alert('✅ Siniestro guardado'); e.target.reset(); await cargarSiniestros(); }
    });

    // Conductores
    document.getElementById('formConductor')?.addEventListener('submit', async (e)=>{
      e.preventDefault();
      const d = {
        nombre: document.getElementById('cond_nombre').value.trim(),
        apellido: document.getElementById('cond_apellido').value.trim() || null,
        rut: document.getElementById('cond_rut').value.trim() || null,
        licencia: document.getElementById('cond_licencia').value.trim() || null
      };
      const res = await safeInsert('conductores', d);
      if(res){ alert('✅ Conductor guardado'); e.target.reset(); await cargarConductores(); await fillVehiculoSelects(); }
    });

    // Conductor Diario (sube fotos)
    document.getElementById('formConductorDiario')?.addEventListener('submit', async (e)=>{
      e.preventDefault();
      const files = document.getElementById('cd_files').files;
      const fotos = await uploadFiles(files, 'conductor_diario');
      const d = {
        vehiculo_id: parseInt(document.getElementById('cd_vehiculo_id').value),
        conductor_id: parseInt(document.getElementById('cd_conductor_id').value),
        fecha: document.getElementById('cd_fecha').value,
        km_inicio: parseInt(document.getElementById('cd_km_inicio').value) || null,
        km_fin: parseInt(document.getElementById('cd_km_fin').value) || null,
        fotos: fotos
      };
      const res = await safeInsert('conductor_diario', d);
      if(res){ alert('✅ Conductor diario guardado'); e.target.reset(); await cargarConductorDiario(); }
    });

    // INIT
    (async ()=>{
      const ok = await testConnection();
      if(ok) await cargarTodo();
      else {
        // intenta cargar selects locales vacíos para evitar errores en la UI
        fillVehiculoSelects();
      }
    })();

  })();
  </script>
</body>
</html>
