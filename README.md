<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gestión de Vehículos DSPC</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { background-color: #f8f9fa; }
    header {
      background: url("ram700.jpg") no-repeat center center;
      background-size: cover;
      height: 250px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
      text-shadow: 2px 2px 4px black;
    }
    header h1 { font-size: 2.5rem; font-weight: bold; }
    .nav-tabs .nav-link.active { background-color: #800040; color: white; }
  </style>
</head>
<body>
  <!-- Portada -->
  <header>
    <h1>🚗 Gestión de Vehículos y Mantención DSPC</h1>
  </header>

  <div class="container my-4">
    <!-- Estado conexión -->
    <div class="text-end mb-3">
      <span id="supabaseStatus" class="badge bg-secondary">Conexión: comprobando...</span>
    </div>

    <!-- Menú -->
    <ul class="nav nav-tabs" id="mainTabs" role="tablist">
      <li class="nav-item"><a class="nav-link active" data-bs-toggle="tab" href="#vehiculos">Vehículos</a></li>
      <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#mantenciones">Mantenciones</a></li>
      <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#limpiezas">Limpiezas</a></li>
      <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#siniestros">Siniestros</a></li>
      <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#conductores">Conductores</a></li>
      <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#conductorDiario">Conductor Diario</a></li>
    </ul>

    <!-- Contenido pestañas -->
    <div class="tab-content p-3 bg-white border border-top-0 rounded-bottom shadow-sm">
      <!-- Vehículos -->
      <div class="tab-pane fade show active" id="vehiculos">
        <form id="formVehiculo" class="card card-body mb-3">
          <h5>Registrar Vehículo</h5>
          <input class="form-control mb-2" id="patente" placeholder="Patente" required>
          <input class="form-control mb-2" id="marca" placeholder="Marca" required>
          <input class="form-control mb-2" id="modelo" placeholder="Modelo" required>
          <input class="form-control mb-2" id="anio" type="number" placeholder="Año" required>
          <input class="form-control mb-2" id="km" type="number" placeholder="Kilometraje">
          <input class="form-control mb-2" id="responsable" placeholder="Responsable">
          <button class="btn btn-primary">Guardar</button>
        </form>
        <table class="table table-striped" id="tablaVehiculos">
          <thead><tr><th>ID</th><th>Patente</th><th>Marca</th><th>Modelo</th><th>Año</th><th>KM</th><th>Responsable</th><th>Registro</th></tr></thead>
          <tbody></tbody>
        </table>
      </div>
      <!-- Mantenciones -->
      <div class="tab-pane fade" id="mantenciones">
        <p>Aquí formulario y tabla de mantenciones</p>
      </div>
      <!-- Limpiezas -->
      <div class="tab-pane fade" id="limpiezas">
        <p>Aquí formulario y tabla de limpiezas</p>
      </div>
      <!-- Siniestros -->
      <div class="tab-pane fade" id="siniestros">
        <p>Aquí formulario y tabla de siniestros</p>
      </div>
      <!-- Conductores -->
      <div class="tab-pane fade" id="conductores">
        <p>Aquí formulario y tabla de conductores</p>
      </div>
      <!-- Conductor Diario -->
      <div class="tab-pane fade" id="conductorDiario">
        <p>Aquí formulario y tabla de conductor diario</p>
      </div>
    </div>
  </div>

  <!-- Scripts -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
  <script>
    // Configuración Supabase
    const SUPABASE_URL = "https://qnufcrywbncbeosfmwgc.supabase.co";
    const SUPABASE_ANON_KEY = "👉 PEGA AQUÍ TU API KEY ENTRE COMILLAS 👈";
    const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

    const statusEl = document.getElementById('supabaseStatus');
    function setStatus(text, cls) {
      statusEl.textContent = text;
      statusEl.className = 'badge ' + cls;
    }

    // Verificar conexión
    async function testConnection() {
      try {
        const { error } = await supabaseClient.from('vehiculos').select('id').limit(1);
        if (error) setStatus("Conexión ✕", "bg-danger");
        else setStatus("Conexión ✓", "bg-success");
      } catch { setStatus("Conexión ✕", "bg-danger"); }
    }
    document.addEventListener('DOMContentLoaded', () => { testConnection(); cargarVehiculos(); });

    // Guardar vehículo
    document.getElementById("formVehiculo").addEventListener("submit", async (e) => {
      e.preventDefault();
      const d = {
        patente: patente.value,
        marca: marca.value,
        modelo: modelo.value,
        anio: parseInt(anio.value),
        km: parseInt(km.value) || null,
        responsable: responsable.value
      };
      const { error } = await supabaseClient.from("vehiculos").insert([d]);
      if (error) { alert("❌ " + error.message); return; }
      alert("✅ Vehículo guardado");
      e.target.reset();
      cargarVehiculos();
    });

    // Cargar vehículos
    async function cargarVehiculos() {
      const { data, error } = await supabaseClient.from("vehiculos").select("*").order("id", { ascending: true });
      if (error) return;
      const tbody = document.querySelector("#tablaVehiculos tbody");
      tbody.innerHTML = "";
      data.forEach(v => {
        tbody.innerHTML += `<tr>
          <td>${v.id}</td><td>${v.patente}</td><td>${v.marca}</td><td>${v.modelo}</td>
          <td>${v.anio}</td><td>${v.km||""}</td><td>${v.responsable||""}</td><td>${v.fecha_registro||""}</td>
        </tr>`;
      });
    }
  </script>
</body>
</html>
