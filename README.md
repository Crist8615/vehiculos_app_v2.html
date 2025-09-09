<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gestión de Vehículos</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { background-color: #f5f5f5; }
    h1 { color: #800040; }
    header {
      background: #800040;
      color: white;
      padding: 30px 15px;
      text-align: center;
    }
  </style>
</head>
<body>
  <header>
    <h1>🚗 Gestión de Vehículos</h1>
    <span id="supabaseStatus" class="badge bg-secondary">Conexión: comprobando...</span>
    <button id="btnTestSupabase" type="button" class="btn btn-sm btn-light ms-2">Probar conexión</button>
  </header>

  <div class="container my-4">
    <form id="formVehiculo" class="card card-body shadow-sm">
      <h5>Registrar Vehículo</h5>
      <label class="form-label">Patente</label>
      <input id="patente" name="patente" class="form-control mb-2" required>
      <label class="form-label">Marca</label>
      <input id="marca" name="marca" class="form-control mb-2" required>
      <label class="form-label">Modelo</label>
      <input id="modelo" name="modelo" class="form-control mb-2" required>
      <label class="form-label">Año</label>
      <input id="anio" type="number" name="anio" class="form-control mb-2" required>
      <label class="form-label">Conductor</label>
      <input id="conductor" name="conductor" class="form-control mb-2">
      <button class="btn btn-primary">Guardar</button>
    </form>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
  <script>
    // 👇 Configura aquí tu conexión
    const SUPABASE_URL = "https://qnufcrywbncbeosfmwgc.supabase.co"; 
    const SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InFudWZjcnl3Ym5jYmVvc2Ztd2djIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NTczNzU4MDEsImV4cCI6MjA3Mjk1MTgwMX0.Ag37eDgN-88h1uvTln9V40CPKSkZvyBF5j-u0EM46mM"; 
    
    const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

    const statusEl = document.getElementById('supabaseStatus');
    const btnTest = document.getElementById('btnTestSupabase');

    function setStatus(text, cls) {
      statusEl.textContent = text;
      statusEl.className = 'badge ' + cls;
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

    btnTest.addEventListener('click', testConnection);
    document.addEventListener('DOMContentLoaded', testConnection);

    // 🚘 Guardar vehículo
    document.getElementById('formVehiculo').addEventListener('submit', async (e) => {
      e.preventDefault();
      const formData = Object.fromEntries(new FormData(e.target).entries());
      formData.anio = Number(formData.anio);

      const { error } = await supabaseClient.from('vehiculos').insert([formData]);
      if (error) {
        alert("❌ Error: " + error.message);
      } else {
        alert("✅ Vehículo guardado");
        e.target.reset();
      }
    });
  </script>
</body>
</html>
