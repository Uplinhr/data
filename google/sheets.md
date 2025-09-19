# Google Sheets – Gestión de Datos  

Dentro del ecosistema de Google Workspace, **Google Sheets** cumplió un rol central como **base de datos operativa** del proyecto. Sirvió tanto para organizar inscripciones y clientes, como para actuar de **backend ligero** en la publicación de información en la landing page.  

---

## Tablas principales utilizadas  

1. **Tabla de postulaciones a vacantes**  
   - Contiene todos los registros provenientes de formularios de postulación a vacantes específicas o a la base de datos general de la bolsa de trabajo.  
   - Información clave: nombre, apellido, correo, CV adjunto, puesto deseado, skills.  

2. **Tabla de vacantes ofrecidas (backend ligero)**  
   - Registro de todas las vacantes publicadas por UPLIN HR.  
   - Esta tabla actúa como **backend ligero**, ya que permite que en el **frontend** aparezcan “cards” con un enlace directo al formulario de postulación, **sin necesidad de redeploy técnico**.  
   - Uso clave: agilidad para actualizar vacantes de forma inmediata.  

3. **Tabla de inscripciones a webinars**  
   - Un único archivo con múltiples hojas internas, cada una asociada a un webinar diferente.  
   - Permite segmentar registros por evento y consolidar métricas de asistencia.  

4. **Tabla de inscripciones a charlas grabadas**  
   - Similar a la tabla de webinars, pero orientada a charlas asincrónicas.  
   - Contiene múltiples hojas, cada una para una charla específica.  

5. **Tabla de clientes B2B**  
   - Registro de empresas y personas que adquieren servicios, principalmente relacionados con **búsqueda de talento**.  
   - Información clave: datos fiscales, contactos principales, tipo de producto adquirido.  

---

## Transformación de datos con fórmulas  

Dado que systeme.io entrega en ocasiones datos **codificados**, se aplicaron fórmulas en Sheets para transformarlos en información legible.  

### 1. Conversión de códigos ISO a nombre de país  

```excel
=ARRAYFORMULA(SI(D2:D=""; ""; BUSCARV(D2:D; Codigos!A:B; 2; FALSO)))
```

- Transforma códigos de país ISO (ej. `AR`) en el nombre completo (`Argentina`).  
- Utiliza una hoja auxiliar llamada **Codigos** con la relación código → nombre.  

---

### 2. Conversión de marcas temporales al huso horario de Argentina  

```excel
=ARRAYFORMULA(
  SI(E2:E="";""; 
    FECHA(VALOR(IZQUIERDA(E2:E;4)); VALOR(EXTRAE(E2:E;6;2)); VALOR(EXTRAE(E2:E;9;2)))
    + VALOR(EXTRAE(E2:E;12;2))/24
    + VALOR(EXTRAE(E2:E;15;2))/1440
    + VALOR(EXTRAE(E2:E;18;2))/86400
    - 3/24
  )
)
```

- Convierte las marcas temporales generadas por systeme.io al huso horario de **Argentina (UTC-3)**.  
- El resultado final muestra fecha y hora en un formato local correcto.  

---

## Conclusiones  

- **Google Sheets** permitió centralizar la información y mantenerla accesible para todo el equipo.  
- Su flexibilidad como **backend ligero** ahorró despliegues técnicos innecesarios.  
- El uso de fórmulas avanzadas resolvió problemas de codificación de systeme.io y mejoró la legibilidad de los datos.  

---
