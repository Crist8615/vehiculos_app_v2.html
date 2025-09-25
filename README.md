<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gestión de Vehículos</title>

  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">

  <!-- 🔹 Menú de navegación -->
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-top shadow">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">🚓 Vehículos DSPC</a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav ms-auto">
          <li class="nav-item"><a class="nav-link active" href="#">Inicio</a></li>
          <li class="nav-item"><a class="nav-link" href="#formulario">Agregar Vehículo</a></li>
          <li class="nav-item"><a class="nav-link" href="#tabla">Ver Registros</a></li>
        </ul>
      </div>
    </div>
  </nav>

  <!-- 🔹 Contenido principal -->
  <div class="container" style="margin-top:100px;">
    <h1 class="mb-4 text-center">Gestión de Vehículos DSPC</h1>

    <!-- Formulario -->
    <div id="formulario" class="card shadow p-4 mb-4">
      <h4 class="mb-3">Agregar nuevo vehículo</h4>
      <form id="vehiculoForm">
        <div class="mb-3">
          <label class="form-label">Patente</label>
          <input type="text" class="form-control" id="patente" required>
        </div>
        <div class="mb-3">
          <label class="form-label">Marca</label>
          <input type="text" class="form-control" id="marca" required>
        </div>
        <div class="mb-3">
          <label class="form-label">Modelo</label>
          <input type="text" class="form-control" id="modelo" required>
        </div>
        <div class="mb-3">
          <label class="form-label">Año</label>
          <input type="number" class="form-control" id="anio" required>
        </div>
        <button type="submit" class="btn btn-primary w-100">Guardar</button>
      </form>
    </div>

    <!-- Tabla -->
    <div id="tabla" class="card shadow p-4">
      <h4 class="mb-3">Listado de Vehículos</h4>
      <table class="table table-bordered table-striped" id="vehiculosTable">
        <thead class="table-dark">
          <tr>
            <th>ID</th>
            <th>Patente</th>
            <th>Marca</th>
            <th>Modelo</th>
            <th>Año</th>
          </tr>
        </thead>
        <tbody></tbody>
      </table>
    </div>
  </div>

  <!-- Bootstrap JS -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
  <!-- Supabase -->
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

  <script>
    // 🔹 Conexión Supabase
    const SUPABASE_URL = "https://qnufcrywbncbeosfmwgc.supabase.co";
    const SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InFudWZjcnl3Ym5jYmVvc2Ztd2djIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NTczNzU4MDEsImV4cCI6MjA3Mjk1MTgwMX0.Ag37eDgN-88h1uvTln9V40CPKSkZvyBF5j-u0EM46mM";
    const supabase = window.supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

    // 🔹 Cargar datos al iniciar
    async function cargarVehiculos() {
      const { data, error } = await supabase.from("vehiculos").select("*").order("id", { ascending: true });
      if (error) {
        console.error("Error cargando vehículos:", error);
        return;
      }
      const tbody = document.querySelector("#vehiculosTable tbody");
      tbody.innerHTML = "";
      data.forEach(v => {
        tbody.innerHTML += `
          <tr>
            <td>${v.id}</td>
            <td>${v.patente}</td>
            <td>${v.marca}</td>
            <td>${v.modelo}</td>
            <td>${v.anio}</td>
          </tr>
        `;
      });
    }

    // 🔹 Guardar nuevo vehículo
    document.getElementById("vehiculoForm").addEventListener("submit", async (e) => {
      e.preventDefault();
      const patente = document.getElementById("patente").value;
      const marca = document.getElementById("marca").value;
      const modelo = document.getElementById("modelo").value;
      const anio = parseInt(document.getElementById("anio").value);

      const { error } = await supabase.from("vehiculos").insert([{ patente, marca, modelo, anio }]);

      if (error) {
        alert("❌ Error al guardar: " + error.message);
      } else {
        alert("✅ Vehículo agregado correctamente");
        e.target.reset();
        cargarVehiculos();
      }
    });

    // Cargar datos al abrir la página
    cargarVehiculos();
  </script>
</body>
</html>
