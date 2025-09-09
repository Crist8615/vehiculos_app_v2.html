<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gestión de Vehículos - DSPC</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { background-color: #f5f5f5; }
    h1 { color: #800040; }
    .required::after { content:" *"; color:red; }
  </style>
</head>
<body>
  <div class="container my-4">
    <h1 class="text-center mb-4">🚗 Gestión de Vehículos y Mantención</h1>

    <!-- Estado de conexión -->
    <div class="text-end mb-2">
      <span id="supabaseStatus" class="badge bg-secondary">Conexión: comprobando...</span>
      <button id="btnTestSupabase" type="button" class="btn btn-sm btn-outline-secondary">Probar conexión</button>
    </div>

    <!-- Formulario -->
    <form id="formVehiculo" class="card card-body shadow-sm mb-3">
      <h5>Registrar Vehículo</h5>
      <label class="form-label required">Patente</label>
      <input id="patente" name="patente" class="form-control mb-2" required>
      <label class="form-label required">Marca</label>
      <input id="marca" name="marca" class="form-control mb-2" required>
      <label class="form-label required">Modelo</label>
      <input id="modelo" name="modelo" class="form-control mb-2" required>
      <label class="form-label required">Año</label>
      <input id="año" type="number" name="año" class="form-control mb-2" min="1980" max="2100" required>
      <label class="form-label">Conductor</label>
      <input id="conductor" name="conductor" class="form-control mb-3">
      <button class="btn btn-primary">Guardar</button>
    </form>

    <!-- Tabla -->
    <table class="table table-striped" id="tablaVehiculos">
      <thead>
        <tr>
          <th>ID</th><th>Patente</th><th>Marca</th><th>Modelo</th><th>Año</th><th>Conductor</th><th>Fecha Registro</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>

  <!-- Scripts -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
  <script>
    // Configuración Supabase
    const SUPABASE_URL = "https://qnufcrywbncbeosfmwgc.supabase.co";
    const SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InFudWZjcnl3Ym5jYmVvc2Ztd2djIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NTczNzU4MDEsImV4cCI6MjA3Mjk1MTgwMX0.Ag37eDgN-88h1uvTln9V40CPKSkZvyBF5j-u0EM46mM";
    const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

    const statusEl = document.getElementById('supabaseStatus');
    const btnTest = document.getElementById('btnTestSupabase');
    const tablaVehiculos = document.querySelector("#tablaVehiculos tbody");

    function setStatus(text, cls) {
      statusEl.textContent = text;
      statusEl.className = 'badge ' + cls;
    }

    // Probar conexión
    async function testConnection() {
      try {
        const { data, error } = await supabaseClient.from('vehiculos').select('id').limit(1);
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
    document.addEventListener('DOMContentLoaded', () => {
      testConnection();
      cargarVehiculos();
    });

    // Guardar vehículo
    document.getElementById("formVehiculo").addEventListener("submit", async (e) => {
      e.preventDefault();
      const d = {
        patente: document.getElementById("patente").value,
        marca: document.getElementById("marca").value,
        modelo: document.getElementById("modelo").value,
        año: parseInt(document.getElementById("año").value),
        conductor: document.getElementById("conductor").value
      };
      const { error } = await supabaseClient.from("vehiculos").insert([d]);
      if (error) {
        alert("❌ Error al guardar: " + error.message);
        console.error(error);
      } else {
        alert("✅ Vehículo guardado en Supabase");
        e.target.reset();
        cargarVehiculos();
      }
    });

    // Cargar vehículos en tabla
    async function cargarVehiculos() {
      const { data, error } = await supabaseClient.from("vehiculos").select("*").order("id", { ascending: true });
      if (error) {
        console.error(error);
        return;
      }
      tablaVehiculos.innerHTML = "";
      data.forEach(v => {
        tablaVehiculos.innerHTML += `
          <tr>
            <td>${v.id}</td>
            <td>${v.patente}</td>
            <td>${v.marca}</td>
            <td>${v.modelo}</td>
            <td>${v.año}</td>
            <td>${v.conductor || ""}</td>
            <td>${v.fecha_registro || ""}</td>
          </tr>
        `;
      });
    }
  </script>
</body>
</html>
