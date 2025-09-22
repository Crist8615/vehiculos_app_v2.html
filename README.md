<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gestión de Vehículos - DSPC</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { background-color: #f5f5f5; }
    h1, h2 { color: #800040; }
    .required::after { content:" *"; color:red; }
    .hero {
      background: url("ram700_hd.jpg") no-repeat center center;
      background-size: cover;
      height: 300px;
      border-radius: 10px;
      margin-bottom: 20px;
    }
    .hero-overlay {
      background: rgba(0,0,0,0.5);
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      border-radius: 10px;
    }
    .hero-overlay h1 {
      color: #fff;
      font-size: 2.5rem;
    }
    .card { margin-bottom: 2rem; }
    .nav-tabs .nav-link.active { background-color: #800040; color: #fff; }
  </style>
</head>
<body>
  <div class="container my-4">

    <!-- Portada RAM -->
    <div class="hero">
      <div class="hero-overlay">
        <h1>🚗 Gestión de Vehículos DSPC</h1>
      </div>
    </div>

    <!-- Estado de conexión -->
    <div class="text-end mb-4">
      <span id="supabaseStatus" class="badge bg-secondary">Conexión: comprobando...</span>
      <button id="btnTestSupabase" type="button" class="btn btn-sm btn-outline-secondary">Probar conexión</button>
    </div>

    <!-- Menú pestañas -->
    <ul class="nav nav-tabs mb-3" id="mainTabs" role="tablist">
      <li class="nav-item"><button class="nav-link active" data-bs-toggle="tab" data-bs-target="#vehiculos">Vehículos</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#mantenciones">Mantenciones</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#limpiezas">Limpiezas</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#siniestros">Siniestros</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#conductores">Conductores</button></li>
      <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#conductor_diario">Conductor Diario</button></li>
    </ul>

    <!-- Contenido de pestañas -->
    <div class="tab-content">

      <!-- Vehículos -->
      <div class="tab-pane fade show active" id="vehiculos">
        <form id="formVehiculo" class="card card-body shadow-sm mb-3">
          <h2>Registrar Vehículo</h2>
          <label class="form-label required">Patente</label>
          <input id="veh_patente" class="form-control mb-2" required>
          <label class="form-label required">Marca</label>
          <input id="veh_marca" class="form-control mb-2" required>
          <label class="form-label required">Modelo</label>
          <input id="veh_modelo" class="form-control mb-2" required>
          <label class="form-label">Año</label>
          <input id="veh_anio" type="number" class="form-control mb-2">
          <label class="form-label">Conductor</label>
          <input id="veh_conductor" class="form-control mb-3">
          <button class="btn btn-primary">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaVehiculos"><thead>
          <tr><th>ID</th><th>Patente</th><th>Marca</th><th>Modelo</th><th>Año</th><th>Conductor</th><th>Fecha</th></tr>
        </thead><tbody></tbody></table>
      </div>

      <!-- Mantenciones -->
      <div class="tab-pane fade" id="mantenciones">
        <form id="formMantencion" class="card card-body shadow-sm mb-3">
          <h2>Registrar Mantención</h2>
          <label class="form-label required">Vehículo ID</label>
          <input id="man_vehiculo_id" type="number" class="form-control mb-2" required>
          <label class="form-label required">Descripción</label>
          <input id="man_desc" class="form-control mb-2" required>
          <label class="form-label">Fecha</label>
          <input id="man_fecha" type="date" class="form-control mb-3">
          <button class="btn btn-primary">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaMantenciones"><thead>
          <tr><th>ID</th><th>Vehículo ID</th><th>Descripción</th><th>Fecha</th></tr>
        </thead><tbody></tbody></table>
      </div>

      <!-- Limpiezas -->
      <div class="tab-pane fade" id="limpiezas">
        <form id="formLimpieza" class="card card-body shadow-sm mb-3">
          <h2>Registrar Limpieza</h2>
          <label class="form-label required">Vehículo ID</label>
          <input id="limp_vehiculo_id" type="number" class="form-control mb-2" required>
          <label class="form-label required">Tipo</label>
          <input id="limp_tipo" class="form-control mb-2" required>
          <label class="form-label">Fecha</label>
          <input id="limp_fecha" type="date" class="form-control mb-3">
          <button class="btn btn-primary">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaLimpiezas"><thead>
          <tr><th>ID</th><th>Vehículo ID</th><th>Tipo</th><th>Fecha</th></tr>
        </thead><tbody></tbody></table>
      </div>

      <!-- Siniestros -->
      <div class="tab-pane fade" id="siniestros">
        <form id="formSiniestro" class="card card-body shadow-sm mb-3">
          <h2>Registrar Siniestro</h2>
          <label class="form-label required">Vehículo ID</label>
          <input id="sin_vehiculo_id" type="number" class="form-control mb-2" required>
          <label class="form-label required">Descripción</label>
          <input id="sin_desc" class="form-control mb-2" required>
          <label class="form-label">Fecha</label>
          <input id="sin_fecha" type="date" class="form-control mb-3">
          <button class="btn btn-primary">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaSiniestros"><thead>
          <tr><th>ID</th><th>Vehículo ID</th><th>Descripción</th><th>Fecha</th></tr>
        </thead><tbody></tbody></table>
      </div>

      <!-- Conductores -->
      <div class="tab-pane fade" id="conductores">
        <form id="formConductor" class="card card-body shadow-sm mb-3">
          <h2>Registrar Conductor</h2>
          <label class="form-label required">Nombre</label>
          <input id="con_nombre" class="form-control mb-2" required>
          <label class="form-label">Licencia</label>
          <input id="con_licencia" class="form-control mb-3">
          <button class="btn btn-primary">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaConductores"><thead>
          <tr><th>ID</th><th>Nombre</th><th>Licencia</th></tr>
        </thead><tbody></tbody></table>
      </div>

      <!-- Conductor Diario -->
      <div class="tab-pane fade" id="conductor_diario">
        <form id="formConductorDiario" class="card card-body shadow-sm mb-3">
          <h2>Asignar Conductor Diario</h2>
          <label class="form-label required">Vehículo ID</label>
          <input id="cd_vehiculo_id" type="number" class="form-control mb-2" required>
          <label class="form-label required">Conductor ID</label>
          <input id="cd_conductor_id" type="number" class="form-control mb-2" required>
          <label class="form-label">Fecha</label>
          <input id="cd_fecha" type="date" class="form-control mb-3">
          <button class="btn btn-primary">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaConductorDiario"><thead>
          <tr><th>ID</th><th>Vehículo ID</th><th>Conductor ID</th><th>Fecha</th></tr>
        </thead><tbody></tbody></table>
      </div>
    </div>
  </div>

  <!-- Scripts -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
  <script>
    // 🔹 Configuración Supabase
    const SUPABASE_URL = "https://qnufcrywbncbeosfmwgc.supabase.co";
    const SUPABASE_ANON_KEY = "👉 PEGA AQUÍ TU API KEY ENTRE COMILLAS 👈";
    const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

    const statusEl = document.getElementById('supabaseStatus');
    function setStatus(text, cls) { statusEl.textContent = text; statusEl.className = 'badge ' + cls; }

    // Probar conexión
    async function testConnection() {
      try {
        const { error } = await supabaseClient.from('vehiculos').select('id').limit(1);
        if (error) { console.error(error); setStatus("Conexión ✕", "bg-danger"); }
        else { setStatus("Conexión ✓", "bg-success"); }
      } catch (e) { console.error(e); setStatus("Conexión ✕", "bg-danger"); }
    }
    document.getElementById('btnTestSupabase').addEventListener('click', testConnection);
    document.addEventListener('DOMContentLoaded', () => { testConnection(); cargarTodo(); });

    // Función genérica para insertar
    async function insertar(tabla, datos) {
      const { error } = await supabaseClient.from(tabla).insert([datos]);
      if (error) { alert("❌ Error: " + error.message); console.error(error); }
      else { alert("✅ Guardado en " + tabla); cargarTodo(); }
    }

    // Vehículos
    document.getElementById("formVehiculo").addEventListener("submit", e=>{
      e.preventDefault();
      insertar("vehiculos", {
        patente: veh_patente.value, marca: veh_marca.value, modelo: veh_modelo.value,
        anio: veh_anio.value, conductor: veh_conductor.value
      }); e.target.reset();
    });

    // Mantenciones
    document.getElementById("formMantencion").addEventListener("submit", e=>{
      e.preventDefault();
      insertar("mantenciones", {
        vehiculo_id: man_vehiculo_id.value, descripcion: man_desc.value, fecha: man_fecha.value
      }); e.target.reset();
    });

    // Limpiezas
    document.getElementById("formLimpieza").addEventListener("submit", e=>{
      e.preventDefault();
      insertar("limpiezas", {
        vehiculo_id: limp_vehiculo_id.value, tipo: limp_tipo.value, fecha: limp_fecha.value
      }); e.target.reset();
    });

    // Siniestros
    document.getElementById("formSiniestro").addEventListener("submit", e=>{
      e.preventDefault();
      insertar("siniestros", {
        vehiculo_id: sin_vehiculo_id.value, descripcion: sin_desc.value, fecha: sin_fecha.value
      }); e.target.reset();
    });

    // Conductores
    document.getElementById("formConductor").addEventListener("submit", e=>{
      e.preventDefault();
      insertar("conductores", { nombre: con_nombre.value, licencia: con_licencia.value });
      e.target.reset();
    });

    // Conductor Diario
    document.getElementById("formConductorDiario").addEventListener("submit", e=>{
      e.preventDefault();
      insertar("conductor_diario", {
        vehiculo_id: cd_vehiculo_id.value, conductor_id: cd_conductor_id.value, fecha: cd_fecha.value
      }); e.target.reset();
    });

    // Función para cargar tablas
    async function cargarTabla(tabla, el, cols) {
      const { data, error } = await supabaseClient.from(tabla).select("*").order("id", { ascending: true });
      if (error) { console.error(error); return; }
      el.innerHTML = "";
      data.forEach(row=>{
        el.innerHTML += "<tr>" + cols.map(c=><td>${row[c]||""}</td>).join("") + "</tr>";
      });
    }

    function cargarTodo() {
      cargarTabla("vehiculos", tablaVehiculos.querySelector("tbody"), ["id","patente","marca","modelo","anio","conductor","fecha_registro"]);
      cargarTabla("mantenciones", tablaMantenciones.querySelector("tbody"), ["id","vehiculo_id","descripcion","fecha"]);
      cargarTabla("limpiezas", tablaLimpiezas.querySelector("tbody"), ["id","vehiculo_id","tipo","fecha"]);
      cargarTabla("siniestros", tablaSiniestros.querySelector("tbody"), ["id","vehiculo_id","descripcion","fecha"]);
      cargarTabla("conductores", tablaConductores.querySelector("tbody"), ["id","nombre","licencia"]);
      cargarTabla("conductor_diario", tablaConductorDiario.querySelector("tbody"), ["id","vehiculo_id","conductor_id","fecha"]);
    }
  </script>
</body>
</html>
