<!DOCTYPE html>
<html lang="es">
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
    header {
      background: url('https://cdn.motor1.com/images/mgl/Mo9V7J/s1/ram-700-2025.jpg') center/cover no-repeat;
      color: white;
      padding: 60px 20px;
      text-align: center;
      border-radius: 0 0 20px 20px;
    }
    header h1 {
      background: rgba(0,0,0,0.6);
      display: inline-block;
      padding: 10px 20px;
      border-radius: 12px;
    }
  </style>
</head>
<body>
  <!-- Portada -->
  <header>
    <h1>🚗 Gestión Profesional de Vehículos - RAM 700 2025</h1>
    <br>
    <span id="supabaseStatus" class="badge bg-secondary ms-2">Conexión: comprobando...</span>
    <button id="btnTestSupabase" type="button" class="btn btn-sm btn-outline-light ms-2">Probar conexión</button>
  </header>

  <div class="container my-4">
    <!-- Formulario Vehículos -->
    <form id="formVehiculo" class="card card-body shadow-sm mb-3">
      <h5>Registrar Vehículo</h5>
      <label class="form-label required">Patente</label>
      <input id="patente" name="patente" class="form-control mb-2" required>
      <label class="form-label required">Marca</label>
      <input id="marca" name="marca" class="form-control mb-2" required>
      <label class="form-label required">Modelo</label>
      <input id="modelo" name="modelo" class="form-control mb-2" required>
      <label class="form-label required">Año</label>
      <input id="anio" type="number" name="anio" class="form-control mb-2" min="1980" max="2100" required>
      <label class="form-label">Conductor Responsable</label>
      <input id="conductor" name="conductor" class="form-control mb-3">
      <button class="btn btn-primary">Guardar</button>
    </form>
  </div>

  <!-- Scripts -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
  <script>
    // 🔗 Conexión a Supabase
    const SUPABASE_URL = "https://qnufcrywbncbeosfmwgc.supabase.co";
    const SUPABASE_ANON_KEY = "TU_SUPABASE_ANON_KEY"; // 👈 reemplaza esto con tu Anon Public Key
    const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

    const statusEl = document.getElementById('supabaseStatus');
    const btnTest = document.getElementById('btnTestSupabase');

    function setStatus(text, cls) {
      statusEl.textContent = text;
      statusEl.className = 'badge ms-2 ' + cls;
    }

    // ✅ Probar conexión
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

    if (btnTest) btnTest.addEventListener('click', testConnection);
    document.addEventListener('DOMContentLoaded', testConnection);

    // 🚘 Guardar vehículo
    const formVehiculo = document.getElementById('formVehiculo');
    formVehiculo.addEventListener('submit', async (e) => {
      e.preventDefault();
      const d = Object.fromEntries(new FormData(formVehiculo).entries());
      d.anio = Number(d.anio || 0);

      try {
        const { error } = await supabaseClient.from('vehiculos').insert([d]);
        if (error) {
          alert("❌ Error al guardar: " + error.message);
        } else {
          alert("✅ Vehículo guardado en Supabase");
          formVehiculo.reset();
        }
      } catch (err) {
        alert("❌ Error de conexión: " + err);
      }
    });
  </script>
</body>
</html>
