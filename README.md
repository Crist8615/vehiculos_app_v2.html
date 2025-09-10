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
      background: center/cover no-repeat url('ram700_banner.jpg');
      display:flex; align-items:center; justify-content:center;
      color:#fff; text-shadow:0 3px 12px rgba(0,0,0,.45);
      border-bottom-left-radius:18px; border-bottom-right-radius:18px;
    }
    header h1 { background:rgba(0,0,0,0.45); padding:10px 18px; border-radius:10px; }
    .nav-tabs .nav-link.active{ background:#800040;color:#fff;border-color:#800040;}
    .card{ border-radius:12px; }
    .thumb{ width:70px;height:50px;object-fit:cover;border-radius:6px;border:1px solid #ddd }
    .small-muted{ font-size:.9rem;color:#6b6f76 }
  </style>
</head>
<body>
  <header>
    <h1>DSPC · Gestión Integral de Flota — RAM 700 (2025)</h1>
  </header>

  <main class="container my-4">
    <div class="d-flex justify-content-end mb-2">
      <span id="supabaseStatus" class="badge bg-secondary me-2">Conexión: comprobando...</span>
      <button id="btnTestSupabase" class="btn btn-sm btn-outline-secondary">Probar conexión</button>
    </div>

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
              <input id="anio" type="number" class="form-control mb-2" min="1900" max="2100" required>
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
            <div class="table-responsive" style="max-height:420px;overflow:auto">
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
    // CONFIG — pega tu API key ANON entre las comillas:
    const SUPABASE_URL = "https://qnufcrywbncbeosfmwgc.supabase.co";
    const SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InFudWZjcnl3Ym5jYmVvc2Ztd2djIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NTczNzU4MDEsImV4cCI6MjA3Mjk1MTgwMX0.Ag37eDgN-88h1uvTln9V40CPKSkZvyBF5j-u0EM46mM";
    const supabase = supabaseCreateClient();

    function supabaseCreateClient(){
      // compatible con CDN UMD global
      return window.supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);
    }

    const client = supabase;

    // UI elements
    const statusEl = document.getElementById('supabaseStatus');
    const btnTest = document.getElementById('btnTestSupabase');

    function setStatus(ok){
      if(ok) { statusEl.textContent = "Conexión ✓"; statusEl.className = "badge bg-success"; }
      else { statusEl.textContent = "Conexión ✕"; statusEl.className = "badge bg-danger"; }
    }

    async function testConnection(){
      try {
        const { data, error } = await client.from('vehiculos').select('id').limit(1);
        if(error) { console.error(error); setStatus(false); return false; }
        setStatus(true); return true;
      } catch(e){ console.error(e); setStatus(false); return false; }
    }
    if(btnTest) btnTest.addEventListener('click', ()=>testConnection());

    // UTIL: upload files to storage bucket 'fotos'
    async function uploadFiles(files, pathPrefix){
      if(!files || files.length===0) return [];
      const urls = [];
      for(let i=0;i<Math.min(files.length,3);i++){
        const file = files[i];
        const filename = ${pathPrefix}/${crypto.randomUUID()}_${file.name.replace(/\s/g,'_')};
        const { error } = await client.storage.from('fotos').upload(filename, file, { cacheControl: '3600', upsert: false });
        if(error){ console.error('upload error', error); continue; }
        const publicUrl = client.storage.from('fotos').getPublicUrl(filename).publicUrl;
        urls.push(publicUrl);
      }
      return urls;
    }

    // RENDER helpers: fill selects
    async function fillVehiculoSelects(){
      const { data } = await client.from('vehiculos').select('id,patente,marca,modelo').order('id',{ascending:true});
      const map = {};
      (data||[]).forEach(v=> map[v.id] = ${v.patente} - ${v.marca} ${v.modelo});
      ['mant_vehiculo_id','limp_vehiculo_id','sini_vehiculo_id','cd_vehiculo_id'].forEach(id=>{
        const sel = document.getElementById(id);
        if(!sel) return;
        sel.innerHTML = '<option value="">Seleccione vehículo</option>';
        (data||[]).forEach(v => sel.insertAdjacentHTML('beforeend', <option value="${v.id}">${v.patente} - ${v.marca} ${v.modelo}</option>));
      });
      // also build vehicle table
      renderVehiculosTable(data||[]);
    }

    async function renderVehiculosTable(list){
      const t = document.getElementById('tbodyVehiculos');
      if(!t) return;
      t.innerHTML = '';
      (list||[]).forEach(v=>{
        t.insertAdjacentHTML('beforeend', `<tr>
          <td>${v.id}</td><td>${escapeHtml(v.patente)}</td><td>${escapeHtml(v.marca)}</td><td>${escapeHtml(v.modelo)}</td>
          <td>${v.anio||''}</td><td>${v.km||''}</td><td>${escapeHtml(v.responsable)||''}</td><td>${v.fecha_registro||''}</td>
        </tr>`);
      });
    }

    function escapeHtml(s){ if(!s && s!==0) return ''; return String(s).replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;'); }

    // CARGAR tablas: mantenciones, limpiezas, siniestros, conductores, conductor_diario
    async function cargarMantenciones(){
      const { data } = await client.from('mantenciones').select('id,vehiculo_id,fecha,tipo,kilometraje,costo,observacion').order('fecha',{ascending:false});
      const veh = await mapVehiculos();
      const tbody = document.getElementById('tbodyMantenciones');
      if(!tbody) return;
      tbody.innerHTML = '';
      (data||[]).forEach(m=>{
        tbody.insertAdjacentHTML('beforeend', `<tr>
          <td>${m.id}</td><td>${veh[m.vehiculo_id] || ''}</td><td>${m.fecha}</td><td>${escapeHtml(m.tipo)}</td><td>${m.kilometraje||''}</td><td>${m.costo||''}</td><td>${escapeHtml(m.observacion)||''}</td>
        </tr>`);
      });
      renderChartMantenciones(data||[], veh);
    }
    async function cargarLimpiezas(){
      const { data } = await client.from('limpiezas').select('*').order('fecha',{ascending:false});
      const veh = await mapVehiculos();
      const tbody = document.getElementById('tbodyLimpiezas');
      tbody.innerHTML = '';
      (data||[]).forEach(r=> tbody.insertAdjacentHTML('beforeend', `<tr>
        <td>${r.id}</td><td>${veh[r.vehiculo_id]||''}</td><td>${r.fecha}</td><td>${escapeHtml(r.observacion)||''}</td>
      </tr>`));
    }
    async function cargarSiniestros(){
      const { data } = await client.from('siniestros').select('*').order('fecha',{ascending:false});
      const veh = await mapVehiculos();
      const tbody = document.getElementById('tbodySiniestros');
      tbody.innerHTML = '';
      (data||[]).forEach(s=> {
        const fotosHtml = (s.fotos||[]).map(u=><a href="${u}" target="_blank"><img src="${u}" class="thumb me-1"></a>).join('');
        tbody.insertAdjacentHTML('beforeend', `<tr>
          <td>${s.id}</td><td>${veh[s.vehiculo_id]||''}</td><td>${s.fecha}</td><td>${escapeHtml(s.descripcion)||''}</td><td>${fotosHtml}</td>
        </tr>`);
      });
    }
    async function cargarConductores(){
      const { data } = await client.from('conductores').select('*').order('id',{ascending:true});
      const tbody = document.getElementById('tbodyConductores');
      tbody.innerHTML = '';
      (data||[]).forEach(c=> tbody.insertAdjacentHTML('beforeend', `<tr>
        <td>${c.id}</td><td>${escapeHtml(c.nombre)}</td><td>${escapeHtml(c.apellido)||''}</td><td>${escapeHtml(c.rut)||''}</td><td>${escapeHtml(c.licencia)||''}</td>
      </tr>`));
    }
    async function cargarConductorDiario(){
      const { data } = await client.from('conductor_diario').select('*').order('fecha',{ascending:false});
      const veh = await mapVehiculos();
      const cond = await mapConductores();
      const tbody = document.getElementById('tbodyConductorDiario');
      tbody.innerHTML = '';
      (data||[]).forEach(d=> {
        const fotosHtml = (d.fotos||[]).map(u=><a href="${u}" target="_blank"><img src="${u}" class="thumb me-1"></a>).join('');
        tbody.insertAdjacentHTML('beforeend', `<tr>
          <td>${d.id}</td><td>${veh[d.vehiculo_id]||''}</td><td>${cond[d.conductor_id]||''}</td><td>${d.fecha}</td><td>${fotosHtml}</td>
        </tr>`);
      });
    }

    // helper maps
    async function mapVehiculos(){
      const { data } = await client.from('vehiculos').select('id,patente,marca,modelo');
      const m = {};
      (data||[]).forEach(v => m[v.id] = ${v.patente} (${v.marca} ${v.modelo}));
      return m;
    }
    async function mapConductores(){
      const { data } = await client.from('conductores').select('id,nombre,apellido');
      const m = {};
      (data||[]).forEach(c=> m[c.id] = ${c.nombre} ${c.apellido||''});
      return m;
    }

    // CHART: mantenciones por vehiculo
    let chartMant;
    function renderChartMantenciones(mantList, vehMap){
      const counts = {};
      mantList.forEach(m=> counts[m.vehiculo_id] = (counts[m.vehiculo_id]||0)+1);
      const labels = Object.keys(vehMap).map(id=>vehMap[id]);
      const data = Object.keys(vehMap).map(id=> counts[id]||0);
      const ctx = document.getElementById('chartMantenciones').getContext('2d');
      if(chartMant) chartMant.destroy();
      chartMant = new Chart(ctx, {
        type:'bar',
        data:{ labels, datasets:[{ label:'Mantenciones', data, backgroundColor:'#800040' }]},
        options:{ responsive:true, scales:{ y:{ beginAtZero:true, ticks:{ precision:0 } } } }
      });
    }

    // CARGAR todo
    async function cargarTodo(){
      await fillVehiculoSelects();
      await cargarMantenciones();
      await cargarLimpiezas();
      await cargarSiniestros();
      await cargarConductores();
      await cargarConductorDiario();
    }

    // FORM HANDLERS
    // 1) vehiculos
    document.getElementById('formVehiculo').addEventListener('submit', async (e)=>{
      e.preventDefault();
      const d = {
        patente: document.getElementById('patente').value.trim(),
        marca: document.getElementById('marca').value.trim(),
        modelo: document.getElementById('modelo').value.trim(),
        anio: parseInt(document.getElementById('anio').value) || null,
        km: parseInt(document.getElementById('km').value) || null,
        responsable: document.getElementById('responsable').value.trim()
      };
      const { error } = await client.from('vehiculos').insert([d]);
      if(error){ alert('❌ ' + error.message); console.error(error); return; }
      alert('✅ Vehículo guardado'); e.target.reset(); await cargarTodo();
    });

    // 2) mantenciones
    document.getElementById('formMantencion').addEventListener('submit', async (e)=>{
      e.preventDefault();
      const d = {
        vehiculo_id: parseInt(document.getElementById('mant_vehiculo_id').value),
        fecha: document.getElementById('mant_fecha').value,
        tipo: document.getElementById('mant_tipo').value,
        kilometraje: parseInt(document.getElementById('mant_km').value)||null,
        costo: parseFloat(document.getElementById('mant_costo').value)||null,
        observacion: document.getElementById('mant_obs').value
      };
      const { error } = await client.from('mantenciones').insert([d]);
      if(error){ alert('❌ ' + error.message); console.error(error); return; }
      alert('✅ Mantención guardada'); e.target.reset(); await cargarMantenciones(); await cargarTodo();
    });

    // 3) limpiezas
    document.getElementById('formLimpieza').addEventListener('submit', async (e)=>{
      e.preventDefault();
      const d = {
        vehiculo_id: parseInt(document.getElementById('limp_vehiculo_id').value),
        fecha: document.getElementById('limp_fecha').value,
        responsable: null,
        observacion: document.getElementById('limp_obs').value
      };
      const { error } = await client.from('limpiezas').insert([d]);
      if(error){ alert('❌ ' + error.message); console.error(error); return; }
      alert('✅ Limpieza guardada'); e.target.reset(); await cargarLimpiezas(); await cargarTodo();
    });

    // 4) siniestros (sube fotos)
    document.getElementById('formSiniestro').addEventListener('submit', async (e)=>{
      e.preventDefault();
      const files = document.getElementById('sini_files').files;
      const fotos = await uploadFiles(files, 'siniestros');
      const d = {
        vehiculo_id: parseInt(document.getElementById('sini_vehiculo_id').value),
        fecha: document.getElementById('sini_fecha').value,
        descripcion: document.getElementById('sini_desc').value,
        costo: parseFloat(document.getElementById('sini_costo').value)||null,
        responsable: null,
        fotos: fotos
      };
      const { error } = await client.from('siniestros').insert([d]);
      if(error){ alert('❌ ' + error.message); console.error(error); return; }
      alert('✅ Siniestro guardado'); e.target.reset(); await cargarSiniestros(); await cargarTodo();
    });

    // 5) conductores
    document.getElementById('formConductor').addEventListener('submit', async (e)=>{
      e.preventDefault();
      const d = {
        nombre: document.getElementById('cond_nombre').value,
        apellido: document.getElementById('cond_apellido').value,
        rut: document.getElementById('cond_rut').value,
        licencia: document.getElementById('cond_licencia').value
      };
      const { error } = await client.from('conductores').insert([d]);
      if(error){ alert('❌ ' + error.message); console.error(error); return; }
      alert('✅ Conductor guardado'); e.target.reset(); await cargarConductores(); await cargarTodo();
    });

    // 6) conductor diario (sube fotos)
    document.getElementById('formConductorDiario').addEventListener('submit', async (e)=>{
      e.preventDefault();
      const files = document.getElementById('cd_files').files;
      const fotos = await uploadFiles(files, 'conductor_diario');
      const d = {
        vehiculo_id: parseInt(document.getElementById('cd_vehiculo_id').value),
        conductor_id: parseInt(document.getElementById('cd_conductor_id').value),
        fecha: document.getElementById('cd_fecha').value,
        km_inicio: parseInt(document.getElementById('cd_km_inicio').value)||null,
        km_fin: parseInt(document.getElementById('cd_km_fin').value)||null,
        observacion: null,
        fotos: fotos
      };
      const { error } = await client.from('conductor_diario').insert([d]);
      if(error){ alert('❌ ' + error.message); console.error(error); return; }
      alert('✅ Conductor diario guardado'); e.target.reset(); await cargarConductorDiario(); await cargarTodo();
    });

    // Inicializar: test conexion y cargar datos
    (async ()=>{ const ok = await testConnection(); if(ok) await cargarTodo(); })();

  })();
  </script>
</body>
</html>
