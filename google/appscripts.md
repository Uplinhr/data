# Google Apps Script – Automatizaciones on-demand

Este documento describe los **scripts personalizados** en Google Apps Script que complementan los flujos de Make. Se ejecutan desde **botones en Google Sheets** para realizar acciones on-demand (sin esperar a un disparador externo), como: normalizar datos, enviar correos y mover registros entre hojas.

---

## Cómo se usan (resumen operativo)

1. **Botón en la hoja**: cada proceso tiene un botón asignado (Dibujo/Imagen → “Asignar script…”).
2. **Rango de datos**: los scripts leen filas desde la fila 2 (encabezados en la 1).
3. **Marcadores de estado**: se escribe “Enviado” u otros estados en la columna indicada para evitar duplicados.
4. **Permisos**: al primer uso, Sheets solicitará autorizar envío de correo y acceso a la hoja.

---

## 1) Enviar correo de bienvenida (masivo)

- Recorre la tabla de postulaciones y envía un correo de bienvenida a quienes **no** tengan marcada la columna **L** como “Enviado”.
- Actualiza la columna **L** al terminar cada envío.

```javascript
function enviarCorreoBienvenida() {
  var hoja = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var datos = hoja.getRange(2, 1, hoja.getLastRow() - 1, 12).getValues(); // Columnas A a L

  for (var i = 0; i < datos.length; i++) {
    var nombreCompleto = datos[i][2]; // Columna C: Apellido y Nombre
    var email = datos[i][4];          // Columna E: Correo electrónico
    var estado = datos[i][11];        // Columna L: Correo de Bienvenida

    if (estado !== "Enviado" && email) {
      var asunto = "Gracias por postularte a UPLIN";

      var mensajeHTML = `
        <div style="font-family: Arial, sans-serif; font-size: 15px; color: #333;">
          <p>Hola <strong>${nombreCompleto}</strong>,</p>

          <p>Gracias por enviar tu CV para futuras oportunidades a través de <strong>UPLIN</strong>. Tu perfil ya forma parte de nuestra base de datos.</p>

          <p>Puedes registrarte en 
          <a href="https://www.uplinhr.com" style="color: #3366cc; text-decoration: none;" target="_blank">
          www.uplinhr.com</a> para acceder a cursos y a diferentes ofertas de empleo.</p>

          <p style="color: #00796b; font-weight: bold; margin-top: 30px;">¡Te deseamos muchos éxitos!</p>

          <p>El equipo de <strong>UPLIN</strong></p>
        </div>
      `;

      MailApp.sendEmail({
        to: email,
        subject: asunto,
        htmlBody: mensajeHTML
      });

      hoja.getRange(i + 2, 12).setValue("Enviado"); // Marca "Enviado" en columna L
    }
  }
}
```

**Uso sugerido**: asignar este script a un botón con etiqueta “Enviar bienvenida”.

---

## 2) Mover tareas resueltas a la hoja “Realizados” (conservando hipervínculos)

- En la hoja **Pendientes**, si la columna **C** (checkbox “Resuelto”) está marcada, **mueve** la fila a **Realizados**.
- Copia valores y preserva el **hipervínculo** de la columna **H**.

```javascript
function moverResueltosConHipervinculo() {
  const libro = SpreadsheetApp.getActiveSpreadsheet();
  const hojaPendientes = libro.getSheetByName("Pendientes");
  const hojaRealizados = libro.getSheetByName("Realizados");

  const ultimaFila = hojaPendientes.getLastRow();
  const ultimaCol = hojaPendientes.getLastColumn();

  const datos = hojaPendientes.getRange(2, 1, ultimaFila - 1, ultimaCol).getValues(); // Datos normales
  const textosRicos = hojaPendientes.getRange(2, 1, ultimaFila - 1, ultimaCol).getRichTextValues(); // Para hipervínculos

  let filasAEliminar = [];

  for (let i = 0; i < datos.length; i++) {
    const fila = datos[i];
    const resuelto = fila[2]; // Columna C: Resuelto (checkbox)

    if (resuelto === true) {
      const nuevaFila = hojaRealizados.getLastRow() + 1;

      // Copia valores planos normalmente
      hojaRealizados.getRange(nuevaFila, 1, 1, ultimaCol).setValues([fila]);

      // Copia hipervínculo si existe en columna H (índice 7)
      const respuestaRich = textosRicos[i][7]; // Columna H (índice base 0)
      if (respuestaRich.getLinkUrl()) {
        hojaRealizados.getRange(nuevaFila, 8).setRichTextValue(respuestaRich);
      }

      filasAEliminar.push(i + 2); // Marca para eliminar (compensa encabezado)
    }
  }

  // Elimina desde abajo hacia arriba
  for (let j = filasAEliminar.length - 1; j >= 0; j--) {
    hojaPendientes.deleteRow(filasAEliminar[j]);
  }
}
```

**Uso sugerido**: botón “Mover resueltos → Realizados”.

---

## 3) Enviar correo de rechazo a postulantes no aceptados (por hoja)

> Se utiliza **un script por cada hoja de vacante**, para responder masivamente a postulantes con estado “No”.  
> Cada función escribe “Enviado” en su columna de control.

### 3.1 Analista de Marketing (Chile)

```javascript
function enviarCorreoRechazo() {
  var hoja = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Analista de Marketing (Chile)");
  var datos = hoja.getRange(2, 1, hoja.getLastRow() - 1, 13).getValues(); // A2:M

  for (var i = 0; i < datos.length; i++) {
    var nombreCompleto = datos[i][3]; // Columna D: Apellido y Nombre
    var email = datos[i][2];          // Columna C: Correo electrónico
    var estado = datos[i][11];        // Columna L: "Pasa"
    var enviado = datos[i][12];       // Columna M: "Correo Electrónico rechazo"

    if (estado === "No" && (!enviado || enviado === "")) {
      var asunto = "Gracias por postular a UPLIN - Proceso de selección";

      var mensajeHTML = `
        <div style="font-family: Arial, sans-serif; font-size: 15px; color: #333;">
          <p>Hola <strong>${nombreCompleto}</strong>,</p>

          <p>Queremos agradecerte sinceramente por el interés que mostraste en postular a la vacante que estamos gestionando y por el tiempo que dedicaste a enviarnos tu hoja de vida. 🙏</p>

          <p>Tras revisar cuidadosamente tu perfil, en esta oportunidad no podremos avanzar contigo en el proceso, ya que estamos buscando una experiencia muy específica en el sector y/o que la persona resida en Santiago de Chile para cumplir con los requisitos de la posición.</p>

          <p>Esto no significa que no veamos valor en tu trayectoria —al contrario— tu perfil nos parece interesante y lo guardaremos para futuras oportunidades que puedan ajustarse mejor a tu experiencia y expectativas.</p>

          <p>Te animamos a seguir nuestras redes sociales para que puedas estar al tanto de nuevas vacantes y oportunidades:</p>
          <ul>
            <li><a href="https://www.linkedin.com/company/uplinhr" target="_blank">🔗 LinkedIn Uplin</a></li>
            <li><a href="https://www.instagram.com/uplinhr" target="_blank">🔗 Instagram Uplin</a></li>
          </ul>

          <p>Te deseamos muchos éxitos en tu carrera profesional y en tu próxima búsqueda de retos.</p>
          <p>Gracias nuevamente por confiar en nosotros.</p>

          <p>Con aprecio,<br><strong>Uplin Team</strong></p>
        </div>
      `;

      MailApp.sendEmail({
        to: email,
        subject: asunto,
        htmlBody: mensajeHTML
      });

      hoja.getRange(i + 2, 13).setValue("Enviado"); // Columna M = 13
    }
  }
}
```

### 3.2 Ejecutivo Comercial Sr. (Chile)

```javascript
function enviarCorreoRechazoEjecutivo() {
  var hoja = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Ejecutivo Comercial Sr. (Chile)");
  var datos = hoja.getRange(2, 1, hoja.getLastRow() - 1, 16).getValues(); // A2:P (columnas 1 a 16)

  for (var i = 0; i < datos.length; i++) {
    var nombreCompleto = datos[i][3]; // Columna D
    var email = datos[i][2];          // Columna C
    var estado = datos[i][11];        // Columna L "Pasa"
    var enviado = datos[i][15];       // Columna P "Correo electrónico (rechazo)"

    if (estado === "No" && (!enviado || enviado === "")) {
      var asunto = "Gracias por postular a UPLIN – Proceso de selección";

      var mensajeHTML = `
        <div style="font-family: Arial, sans-serif; font-size: 15px; color: #333;">
          <p>Hola <strong>${nombreCompleto}</strong>,</p>

          <p>Queremos agradecerte sinceramente por el interés que mostraste en postular a la vacante que estamos gestionando y por el tiempo que dedicaste a enviarnos tu hoja de vida. 🙏</p>

          <p>Tras revisar cuidadosamente tu perfil, en esta oportunidad no podremos avanzar contigo en el proceso, ya que estamos buscando una experiencia muy específica en el sector y/o que la persona resida en Santiago de Chile para cumplir con los requisitos de la posición.</p>

          <p>Esto no significa que no veamos valor en tu trayectoria —al contrario— tu perfil nos parece interesante y lo guardaremos para futuras oportunidades que puedan ajustarse mejor a tu experiencia y expectativas.</p>

          <p>Te animamos a seguir nuestras redes sociales para que puedas estar al tanto de nuevas vacantes y oportunidades:</p>
          <ul>
            <li><a href="https://www.linkedin.com/company/uplinhr" target="_blank">🔗 LinkedIn Uplin</a></li>
            <li><a href="https://www.instagram.com/uplinhr" target="_blank">🔗 Instagram Uplin</a></li>
          </ul>

          <p>Te deseamos muchos éxitos en tu carrera profesional y en tu próxima búsqueda de retos.</p>

          <p>Gracias nuevamente por confiar en nosotros.</p>

          <p>Con aprecio,<br><strong>Uplin Team</strong></p>
        </div>
      `;

      MailApp.sendEmail({
        to: email,
        subject: asunto,
        htmlBody: mensajeHTML
      });

      hoja.getRange(i + 2, 16).setValue("Enviado"); // Marca como "Enviado" en la columna P (índice 16)
    }
  }
}
```

### 3.3 Líder Comercial (México)

```javascript
function enviarCorreoRechazoMexico() {
  var hoja = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Lider Comercial (MEXICO)");
  var datos = hoja.getRange(2, 1, hoja.getLastRow() - 1, 14).getValues(); // A2:N

  for (var i = 0; i < datos.length; i++) {
    var nombreCompleto = datos[i][3]; // Columna D: Apellido y Nombre
    var email = datos[i][2];          // Columna C: Correo electrónico
    var estado = datos[i][12];        // Columna M: "Pasa"
    var enviado = datos[i][13];       // Columna N: "Correo electrónico (rechazo)"

    if (estado === "No" && (!enviado || enviado === "")) {
      var asunto = "Gracias por postular a UPLIN – Proceso de selección";

      var mensajeHTML = `
        <div style="font-family: Arial, sans-serif; font-size: 15px; color: #333;">
          <p>Hola <strong>${nombreCompleto}</strong>,</p>

          <p>Queremos agradecerte sinceramente por el interés que mostraste en postular a la vacante que estamos gestionando y por el tiempo que dedicaste a enviarnos tu hoja de vida. 🙏</p>

          <p>Tras revisar cuidadosamente tu perfil, en esta oportunidad no podremos avanzar contigo en el proceso, ya que estamos buscando una experiencia muy específica en el sector para cumplir con los requisitos de la posición.</p>

          <p>Esto no significa que no veamos valor en tu trayectoria —al contrario— tu perfil nos parece interesante y lo guardaremos para futuras oportunidades que puedan ajustarse mejor a tu experiencia y expectativas.</p>

          <p>Te animamos a seguir nuestras redes sociales para que puedas estar al tanto de nuevas vacantes y oportunidades:</p>
          <ul>
            <li><a href="https://www.linkedin.com/company/uplinhr" target="_blank">🔗 LinkedIn Uplin</a></li>
            <li><a href="https://www.instagram.com/uplinhr" target="_blank">🔗 Instagram Uplin</a></li>
          </ul>

          <p>Te deseamos muchos éxitos en tu carrera profesional y en tu próxima búsqueda de retos.</p>

          <p>Gracias nuevamente por confiar en nosotros.</p>

          <p>Con aprecio,<br><strong>Uplin Team</strong></p>
        </div>
      `;

      MailApp.sendEmail({
        to: email,
        subject: asunto,
        htmlBody: mensajeHTML
      });

      hoja.getRange(i + 2, 14).setValue("Enviado"); // Marca "Enviado" en columna N
    }
  }
}
```

---

## Notas de implementación

- **Asignar script al botón**: Insertar → Dibujo/Imagen → clic derecho → “Asignar script…” → escribir el nombre de la función (por ejemplo, `enviarCorreoBienvenida`).
- **Columnas y nombres de hoja**: adaptar índices de columnas y `getSheetByName` a cada caso real.
- **Evitar reenvíos**: los marcadores “Enviado” son clave para no repetir correos.
- **Manejo de errores**: opcionalmente, envolver `MailApp.sendEmail` en `try/catch` y registrar fallos en una hoja de Logs.

---

**Conclusión**  
Estos scripts permitieron al equipo ejecutar acciones masivas con un clic, complementando a Make en tareas que requerían **control manual inmediato** desde la propia hoja de cálculo.
