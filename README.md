Te explico paso a paso y claramente cómo está hecho el proyecto, cómo funciona cada parte, y cómo lo puedes conectar a Google Sheets en tiempo real, sin necesidad de backend complicado.


---

✅ ¿Cómo está hecho el proyecto?

Este es un proyecto web 100% HTML, CSS y JavaScript puro, sin frameworks ni bases de datos.

🔹 Estructura general:

1. Inicio.html
Tiene dos botones:

“🚀 Soy Conductor” → Abre formulario para registrarse como conductor.

“📍 Necesito Conductor” → Abre formulario para pedir un viaje.



2. FormularioConductor.html
Formulario para que el conductor registre:

Nombre, apellido, tipo y número de documento, placa de moto.

Se envía a Google Sheets automáticamente.

Redirige a viajes disponibles.



3. FormularioPasajero.html
Formulario simple para pedir un conductor:

Nombre, dirección de recogida, y destino.

También se guarda en Google Sheets.



4. ViajesDisponibles.html
Aquí los conductores ven los viajes disponibles.

Cada tarjeta tiene botón "Aceptar".

Al aceptar: sale mensaje, desactiva botón, y no puede ser aceptado por otro.





---

✅ ¿Cómo implementamos todo?

🧩 CSS: Diseño simple y limpio

Fondos claros (#f8fafc), botones verdes y azules.

Tarjetas .viaje con box-shadow y border-radius para apariencia moderna.

Flecha ⬅ que te lleva al inicio (inicio.html).


🔘 Botón “Aceptar”

Cuando haces clic:

function aceptarViaje(boton, nombre, placa, tiempo) {
  if (boton.disabled) {
    alert("❌ Viaje ya fue tomado por otro conductor.");
    return;
  }

  alert(`✅ Has aceptado el viaje de ${nombre}.\n\n🚀 Su conductor va en camino.\n🚦 Placa: ${placa}\n🕒 Tiempo estimado: ${tiempo}`);
  boton.textContent = "Viaje aceptado";
  boton.disabled = true;
  boton.style.backgroundColor = "#94a3b8";
}

Este código controla la lógica del viaje aceptado para que no se repita.


---

✅ ¿Cómo conectar con Google Sheets?

✨ ¿Qué puedes hacer con Google Sheets?

Guardar registros de conductores ✅

Guardar solicitudes de pasajeros ✅

Mostrar viajes disponibles ✅



---

🛠️ Paso a paso para usar Google Sheets como base de datos:

1. Crea una hoja de cálculo nueva

Entra a Google Sheets

Nómbrala por ejemplo GoMoto


2. Agrega columnas según formulario

Ejemplo para pasajeros:

Nombre | Dirección Recogida | Destino | Timestamp

Ejemplo para conductores:

Nombre | Apellido | Tipo Documento | Número | Placa | Timestamp


---

3. Abre el editor de scripts

Haz clic en:

Extensiones > Apps Script


Pega este código para capturar datos vía POST:

function doPost(e) {
  var hoja = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Hoja 1");
  var data = JSON.parse(e.postData.contents);
  hoja.appendRow([
    data.nombre,
    data.recogida,
    data.destino,
    new Date()
  ]);
  return ContentService.createTextOutput("OK");
}

> Si es para conductores, cambia las columnas del appendRow.




---

4. Despliega como Web App

Haz clic en:

Implementar > Implementar como aplicación web


Configura así:

Ejecutar como: Tú mismo

Acceso: Cualquiera, incluso anónimo


🔗 Copia el enlace del Web App


---

5. Conecta con tu formulario en HTML

Dentro de tu HTML (ejemplo pasajero), usa esto en el <script>:

document.getElementById("formulario").addEventListener("submit", function(e) {
  e.preventDefault();

  const data = {
    nombre: document.getElementById("nombre").value,
    recogida: document.getElementById("recogida").value,
    destino: document.getElementById("destino").value,
  };

  fetch("TU_URL_WEB_APP", {
    method: "POST",
    body: JSON.stringify(data),
  })
  .then(res => {
    alert("✅ Solicitud enviada. Un conductor irá pronto.");
    window.location.href = "inicio.html";
  })
  .catch(err => {
    alert("❌ Error al enviar.");
    console.error(err);
  });
});

> Reemplaza TU_URL_WEB_APP con el enlace que copiaste de Google Sheets.




---

6. Mostrar los viajes desde Google Sheets

Usa otro código para recibir datos como JSON:

function doGet(e) {
  var hoja = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Hoja 1");
  var datos = hoja.getDataRange().getValues();
  var headers = datos.shift();
  var salida = datos.map(fila => {
    var item = {};
    headers.forEach((h, i) => item[h.toLowerCase()] = fila[i]);
    return item;
  });
  return ContentService.createTextOutput(JSON.stringify(salida)).setMimeType(ContentService.MimeType.JSON);
}

Luego, desde tu HTML (por ejemplo viajesDisponibles.html), puedes hacer:

fetch("TU_URL_WEB_APP_GET")
  .then(res => res.json())
  .then(data => {
    // Aquí generas las tarjetas de viajes con .innerHTML
  });


---

🌍 Todo se ambienta en Paz de Ariporo, Casanare

Los nombres de sitios y puntos de referencia (SENA, Hospital, Universidad UNAD, etc.) están basados en esa ciudad. Puedes cambiarlos si quieres otro municipio.


---

✅ ¿Qué más puedes añadir después?

Ver ubicación en mapa (Google Maps o Leaflet.js)

Notificaciones en tiempo real (con Firebase o WebSocket)

Calificaciones de conductores

Chat conductor ↔ pasajero



---
