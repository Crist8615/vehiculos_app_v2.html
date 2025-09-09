<!DOCTYPE html>
<html
  <!-- Indicador de conexión a Supabase -->
<span id="supabaseStatus" class="badge bg-secondary ms-2">Conexión: comprobando...</span>
<button id="btnTestSupabase" type="button" class="btn btn-sm btn-outline-secondary ms-2">Probar conexión</button>lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gestión de Vehículos - DSPC</title>
  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { background-color: #f5f5f5; }
    h1, h5 { color: #800040; }
    .required::after { content:" *"; color:red; }
  </style>
</head>
<body>
  <div class="container my-4">
    <h1 class="text-center mb-4">🚗 Gestión de Vehículos y Mantención</h1>

    <!-- Menú de pestañas -->
    <ul class="nav nav-tabs" id="appTabs" role="tablist">
      <li class="nav-item"><button class="nav-link active" id="vehiculos-tab" data-bs-toggle="tab" data-bs-target="#vehiculos">Vehículos</button></li>
      <li class="nav-item"><button class="nav-link" id="mantenciones-tab" data-bs-toggle="tab" data-bs-target="#mantenciones">Mantenciones</button></li>
      <li class="nav-item"><button class="nav-link" id="limpiezas-tab" data-bs-toggle="tab" data-bs-target="#limpiezas">Limpiezas</button></li>
      <li class="nav-item"><button class="nav-link" id="siniestros-tab" data-bs-toggle="tab" data-bs-target="#siniestros">Siniestros</button></li>
      <li class="nav-item"><button class="nav-link" id="conductores-tab" data-bs-toggle="tab" data-bs-target="#conductores">Conductores</button></li>
      <li class="nav-item"><button class="nav-link" id="conductorDiario-tab" data-bs-toggle="tab" data-bs-target="#conductorDiario">Conductor Diario</button></li>
    </ul>

    <!-- Contenido de las pestañas -->
    <div class="tab-content p-4 border bg-white rounded-bottom" id="appTabsContent">

      <!-- Vehículos -->
      <div class="tab-pane fade show active" id="vehiculos">
        <div class="row">
          <div class="col-lg-5">
            <form id="formVehiculo" class="card card-body shadow-sm mb-3">
              <h5>Registrar Vehículo</h5>
              <label class="form-label required">Patente</label>
              <input name="patente" class="form-control mb-2" required>
              <label class="form-label required">Marca</label>
              <input name="marca" class="form-control mb-2" required>
              <label class="form-label required">Modelo</label>
              <input name="modelo" class="form-control mb-2" required>
              <label class="form-label required">Año</label>
              <input type="number" name="anio" class="form-control mb-2" min="1980" max="2100" required>
              <label class="form-label required">Kilometraje</label>
              <input type="number" name="kilometraje" class="form-control mb-2" min="0" required>
              <label class="form-label">Responsable</label>
              <input name="responsable" class="form-control mb-3">
              <button class="btn btn-primary">Guardar</button>
            </form>
          </div>
          <div class="col-lg-7">
            <table class="table table-striped" id="tablaVehiculos">
              <thead>
                <tr>
                  <th>Patente</th><th>Marca</th><th>Modelo</th><th>Año</th><th>Km</th><th>Responsable</th><th>Próx. Mantención</th>
                </tr>
              </thead>
              <tbody></tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- Mantenciones -->
      <div class="tab-pane fade" id="mantenciones">
        <form id="formMantencion" class="card card-body shadow-sm mb-3">
          <h5>Registrar Mantención</h5>
          <select name="vehiculo_id" class="form-select mb-2" required>
            <option value="">Seleccione un vehículo</option>
          </select>
          <input type="date" name="fecha" class="form-control mb-2" required>
          <select name="tipo" class="form-select mb-2" required>
            <option value="">Tipo de mantención</option>
            <option>Cambio de aceite</option>
            <option>Frenos</option>
            <option>Neumáticos</option>
            <option>Batería</option>
            <option>Alineación y balanceo</option>
            <option>Revisión general</option>
          </select>
          <input type="number" name="kilometraje" class="form-control mb-2" placeholder="Kilometraje" required>
          <input type="number" name="costo" class="form-control mb-2" placeholder="Costo CLP">
          <textarea name="observaciones" class="form-control mb-3" placeholder="Observaciones"></textarea>
          <button class="btn btn-success">Guardar</button>
        </form>

        <table class="table table-striped" id="tablaMantenciones">
          <thead>
            <tr><th>Vehículo</th><th>Fecha</th><th>Tipo</th><th>Km</th><th>Costo</th><th>Obs.</th></tr>
          </thead>
          <tbody></tbody>
        </table>

        <div class="card mt-4 p-3">
          <h5>Gráfica de Mantenciones por Vehículo</h5>
          <canvas id="graficoMantenciones"></canvas>
        </div>
      </div>

      <!-- Limpiezas -->
      <div class="tab-pane fade" id="limpiezas">
        <form id="formLimpieza" class="card card-body shadow-sm mb-3">
          <h5>Registrar Limpieza</h5>
          <select name="vehiculo_id" class="form-select mb-2" required></select>
          <input type="date" name="fecha" class="form-control mb-2" required>
          <label class="form-label">Observaciones</label>
          <textarea name="observaciones" class="form-control mb-3"></textarea>
          <button class="btn btn-info">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaLimpiezas">
          <thead><tr><th>Vehículo</th><th>Fecha</th><th>Obs.</th></tr></thead>
          <tbody></tbody>
        </table>
      </div>

      <!-- Siniestros -->
      <div class="tab-pane fade" id="siniestros">
        <form id="formSiniestro" class="card card-body shadow-sm mb-3">
          <h5>Registrar Siniestro</h5>
          <select name="vehiculo_id" class="form-select mb-2" required></select>
          <input type="date" name="fecha" class="form-control mb-2" required>
          <label class="form-label">Descripción</label>
          <textarea name="descripcion" class="form-control mb-2" required></textarea>
          <label class="form-label">Foto (URL)</label>
          <input type="text" name="foto" class="form-control mb-3" placeholder="https://...">
          <button class="btn btn-warning">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaSiniestros">
          <thead><tr><th>Vehículo</th><th>Fecha</th><th>Descripción</th><th>Foto</th></tr></thead>
          <tbody></tbody>
        </table>
      </div>

      <!-- Conductores -->
      <div class="tab-pane fade" id="conductores">
        <form id="formConductor" class="card card-body shadow-sm mb-3">
          <h5>Registrar Conductor</h5>
          <input name="nombre" class="form-control mb-2" placeholder="Nombre completo" required>
          <input name="rut" class="form-control mb-2" placeholder="RUT">
          <button class="btn btn-secondary">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaConductores">
          <thead><tr><th>Nombre</th><th>RUT</th></tr></thead>
          <tbody></tbody>
        </table>
      </div>

      <!-- Conductor Diario -->
      <div class="tab-pane fade" id="conductorDiario">
        <form id="formConductorDiario" class="card card-body shadow-sm mb-3">
          <h5>Asignar Conductor Diario</h5>
          <select name="vehiculo_id" class="form-select mb-2" required></select>
          <select name="conductor_id" class="form-select mb-2" required></select>
          <input type="date" name="fecha" class="form-control mb-3" required>
          <button class="btn btn-dark">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaConductorDiario">
          <thead><tr><th>Vehículo</th><th>Conductor</th><th>Fecha</th></tr></thead>
          <tbody></tbody>
        </table>
      </div>

    </div>
  </div>

  <!-- Scripts -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script>
    // --- Almacenamiento local ---
    const db={get(k){return JSON.parse(localStorage.getItem(k)||"[]");},set(k,v){localStorage.setItem(k,JSON.stringify(v));}};
    const KEYS={vehiculos:"vehiculos",mantenciones:"mantenciones",limpiezas:"limpiezas",siniestros:"siniestros",conductores:"conductores",conductorDiario:"conductorDiario"};
    function id(){return crypto.randomUUID();}

    // --- VEHÍCULOS ---
    function renderVehiculos(){
      const list=db.get(KEYS.vehiculos);
      const tbody=document.querySelector("#tablaVehiculos tbody");
      tbody.innerHTML="";
      list.forEach(v=>{
        const prox=v.kilometraje?((Math.floor(v.kilometraje/10000)+1)*10000):"";
        tbody.innerHTML+=`<tr>
          <td>${v.patente}</td><td>${v.marca}</td><td>${v.modelo}</td><td>${v.anio}</td>
          <td>${v.kilometraje}</td><td>${v.responsable||""}</td><td>${prox?prox+" km":""}</td>
        </tr>`;
      });
    }
    document.getElementById("formVehiculo").onsubmit=e=>{
      e.preventDefault();
      const d=Object.fromEntries(new FormData(e.target).entries());
      d.id=id();
      db.set(KEYS.vehiculos,[...db.get(KEYS.vehiculos),d]);
      e.target.reset();
      renderVehiculos();
      renderSelectVehiculos();
      renderSelectLimpieza();
      renderSelectSiniestro();
      renderSelectConductorDiario();
      renderGraficoMantenciones();
    }
    renderVehiculos();

    // --- SELECT VEHÍCULOS ---
    function renderSelectVehiculos(){
      const vehiculos=db.get(KEYS.vehiculos);
      document.querySelectorAll("select[name=vehiculo_id]").forEach(sel=>{
        const old=sel.value;
        sel.innerHTML='<option value="">Seleccione un vehículo</option>';
        vehiculos.forEach(v=>{sel.innerHTML+=<option value="${v.id}">${v.patente} - ${v.marca} ${v.modelo}</option>;});
        sel.value=old;
      });
    }
    function renderSelectLimpieza(){renderSelectVehiculos();}
    function renderSelectSiniestro(){renderSelectVehiculos();}
    function renderSelectConductorDiario(){renderSelectVehiculos();}

    // --- MANTENCIONES ---
    function renderMantenciones(){
      const vehiculos=db.get(KEYS.vehiculos).reduce((a,v)=>{a[v.id]=v;return a;},{});
      const list=db.get(KEYS.mantenciones);
      const tbody=document.querySelector("#tablaMantenciones tbody");
      tbody.innerHTML="";
      list.forEach(m=>{
        tbody.innerHTML+=`<tr>
          <td>${vehiculos[m.vehiculo_id]?.patente||""}</td><td>${m.fecha}</td><td>${m.tipo}</td>
          <td>${m.kilometraje}</td><td>${m.costo||""}</td><td>${m.observaciones||""}</td>
        </tr>`;
      });
    }
    document.getElementById("formMantencion").onsubmit=e=>{
      e.preventDefault();
      const d=Object.fromEntries(new FormData(e.target).entries());
      d.id=id();
      db.set(KEYS.mantenciones,[...db.get(KEYS.mantenciones),d]);
      e.target.reset();
      renderMantenciones();
      renderSelectVehiculos();
      renderGraficoMantenciones();
    }
    renderMantenciones();
    renderSelectVehiculos();

    // --- GRAFICO MANTENCIONES ---
    let graficoMantenciones;
    function renderGraficoMantenciones(){
      const vehiculos=db.get(KEYS.vehiculos).reduce((a,v)=>{a[v.id]=v;return a;},{});
      const mantenciones=db.get(KEYS.mantenciones);
      const conteo={};
      mantenciones.forEach(m=>{conteo[m.vehiculo_id]=conteo[m.vehiculo_id]?conteo[m.vehiculo_id]+1:1;});
      const labels=Object.keys(vehiculos).map(id=>vehiculos[id].patente);
      const data=labels.map(patente=>{
        const v=Object.values(vehiculos).find(v=>v.patente===patente);
        return conteo[v.id]||0;
      });
      const ctx=document.getElementById('graficoMantenciones').getContext('2d');
      if(graficoMantenciones) graficoMantenciones.destroy();
      graficoMantenciones=new Chart(ctx,{
        type:'bar',
        data:{labels:labels,datasets:[{label:'Cantidad de Mantenciones',data:data,backgroundColor:'#800040'}]},
        options:{responsive:true,scales:{y:{beginAtZero:true,precision:0}}}
      });
    }
    renderGraficoMantenciones();

    // --- LIMPIEZAS ---
    function renderLimpiezas(){
      const vehiculos=db.get(KEYS.vehiculos).reduce((a,v)=>{a[v.id]=v;return a;},{});
      const list=db.get(KEYS.limpiezas);
      const tbody=document.querySelector("#tablaLimpiezas tbody");
      tbody.innerHTML="";
      list.forEach(l=>{
        tbody.innerHTML+=<tr><td>${vehiculos[l.vehiculo_id]?.patente||""}</td><td>${l.fecha}</td><td>${l.observaciones||""}</td></tr>;
      });
    }
    document.getElementById("formLimpieza").onsubmit=e=>{
      e.preventDefault();
      const d=Object.fromEntries(new FormData(e.target).entries());
      d.id=id();
      db.set(KEYS.limpiezas,[...db.get(KEYS.limpiezas),d]);
      e.target.reset();
      renderLimpiezas();
    }
    renderLimpiezas();

    // --- SINIESTROS ---
    function renderSiniestros(){
      const vehiculos=db.get(KEYS.vehiculos).reduce((a,v)=>{a[v.id]=v;return a;},{});
      const list=db.get(KEYS.siniestros);
      const tbody=document.querySelector("#tablaSiniestros tbody");
      tbody.innerHTML="";
      list.forEach(s=>{
        tbody.innerHTML+=<tr><td>${vehiculos[s.vehiculo_id]?.patente||""}</td><td>${s.fecha}</td><td>${s.descripcion}</td><td>${s.foto?<a href="${s.foto}" target="_blank">Ver</a>:""}</td></tr>;
      });
    }
    document.getElementById("formSiniestro").onsubmit=e=>{
      e.preventDefault();
      const d=Object.fromEntries(new FormData(e.target).entries());
      d.id=id();
      db.set(KEYS.siniestros,[...db.get(KEYS.siniestros),d]);
      e.target.reset();
      renderSiniestros();
    }
    renderSiniestros();

    // --- CONDUCTORES ---
    function renderConductores(){
      const list=db.get(KEYS.conductores);
      const tbody=document.querySelector("#tablaConductores tbody");
      tbody.innerHTML="";
      list.forEach(c=>{tbody.innerHTML+=<tr><td>${c.nombre}</td><td>${c.rut||""}</td></tr>;});
    }
    document.getElementById("formConductor").onsubmit=e=>{
      e.preventDefault();
      const d=Object.fromEntries(new FormData(e.target).entries());
      d.id=id();
      db.set(KEYS.conductores,[...db.get(KEYS.conductores),d]);
      e.target.reset();
      renderConductores();
      renderSelectConductorDiario();
    }
    renderConductores();

    // --- CONDUCTOR DIARIO ---
    function renderConductorDiario(){
      const vehiculos=db.get(KEYS.vehiculos).reduce((a,v)=>{a[v.id]=v;return a;},{});
      const conductores=db.get(KEYS.conductores).reduce((a,v)=>{a[v.id]=v;return a;},{});
      const list=db.get(KEYS.conductorDiario);
      const tbody=document.querySelector("#tablaConductorDiario tbody");
      tbody.innerHTML="";
      list.forEach(d=>{
        tbody.innerHTML+=<tr><td>${vehiculos[d.vehiculo_id]?.patente||""}</td><td>${conductores[d.conductor_id]?.nombre||""}</td><td>${d.fecha}</td></tr>;
      });
    }
    document.getElementById("formConductorDiario").onsubmit=e=>{
      e.preventDefault();
      const d=Object.fromEntries(new FormData(e.target).entries());
      d.id=id();
      db.set(KEYS.conductorDiario,[...db.get(KEYS.conductorDiario),d]);
      e.target.reset();
      renderConductorDiario();
    }
    renderConductorDiario();
  </script>
</body>
<!-- Supabase -->
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<script>
(function(){
  const SUPABASE_URL = "https://qnufcrywbncbeosfmwgc.supabase.co";
  const SUPABASE_ANON_KEY = "REEMPLAZA_CON_TU_ANON_KEY"; // copia tu anon key aquí
  const { createClient } = supabase;
  const supabaseClient = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

  const statusEl = document.getElementById('supabaseStatus');
  const btnTest = document.getElementById('btnTestSupabase');

  function setStatus(text, cls) {
    if(!statusEl) return;
    statusEl.textContent = text;
    statusEl.className = 'badge ms-2 ' + cls;
  }

  async function testConnection() {
    try {
      const { data, error } = await supabaseClient.from('vehiculos').select('patente').limit(1);
      if (error) {
        console.error(error);
        setStatus("Conexión ✕", "bg-danger");
      } else {
        setStatus("Conexión ✓", "bg-success");
      }
    } catch (e) {
      console.error(e);
      setStatus("Conexión ✕", "bg-danger");
    }
  }

  if (btnTest) {
    btnTest.addEventListener('click', testConnection);
  }

  document.addEventListener('DOMContentLoaded', testConnection);

  // --- Guardar vehículo ---
  const formVehiculo = document.getElementById('formVehiculo');
  if (formVehiculo) {
    formVehiculo.addEventListener('submit', async (e) => {
      e.preventDefault();
      const d = Object.fromEntries(new FormData(formVehiculo).entries());
      d.anio = Number(d.anio || 0);
      try {
        const { error } = await supabaseClient.from('vehiculos').insert([d]);
        if (error) {
          console.error(error);
          alert("Error al guardar: " + error.message);
        } else {
          alert("Vehículo guardado en Supabase ✅");
        }
      } catch (err) {
        console.error(err);
        alert("Error de conexión: " + err);
      }
    });
  }
})();
</script>
<!-- ==========================
     Conexión a Supabase
==============================-->
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js"></script>
<script>
  // Inicializar cliente Supabase
  const { createClient } = supabase
  const supabaseUrl = "https://TU-PROYECTO.supabase.co"   // 👈 pega tu URL
  const supabaseKey = "TU_API_KEY"                       // 👈 pega tu anon key
  const db = createClient(supabaseUrl, supabaseKey)

  // Función para guardar mantención
  async function guardarMantenimiento(patente, marca, modelo, anio, conductor) {
    const { data, error } = await db
      .from("vehiculos") // 👈 nombre exacto de tu tabla
      .insert([{ 
        patente, 
        marca, 
        modelo, 
        anio: parseInt(anio), 
        conductor 
      }])

    if (error) {
      alert("❌ Error al guardar: " + error.message)
      console.error(error)
    } else {
      alert("✅ Vehículo guardado en la base de datos")
      console.log(data)
    }
  }

  // Vincular formulario con la función
  document.addEventListener("DOMContentLoaded", () => {
    const form = document.getElementById("formVehiculo")
    if (form) {
      form.addEventListener("submit", (e) => {
        e.preventDefault()
        const patente = document.getElementById("patente").value
        const marca = document.getElementById("marca").value
        const modelo = document.getElementById("modelo").value
        const anio = document.getElementById("anio").value
        const conductor = document.getElementById("conductor").value

        guardarMantenimiento(patente, marca, modelo, anio, conductor)
      })
    }
  })
</script>
</html>
