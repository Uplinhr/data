# Integraciones de Google Workspace  

El uso de **Google Workspace** en UPLIN HR no se limitó a las herramientas individuales (Sheets, Gmail, Drive), sino que se articuló mediante integraciones con **Make** y **systeme.io** para formar un ecosistema automatizado y flexible.  

---

## 1. Conexión con Make (orquestación de flujos)  

- **Entrada:** Gmail sirvió como receptor de correos automáticos enviados por systeme.io.  
- **Transformación:** Make extrajo y normalizó los datos (Regex, Parse JSON).  
- **Salida:** Los registros se cargaban automáticamente en Google Sheets o bases SQL, quedando listos para análisis y uso operativo.  

**Ejemplo de flujo típico:**  
1. Gmail recibe un correo de inscripción a un webinar.  
2. Make detecta el correo entrante.  
3. Se extraen los datos clave (nombre, correo, país).  
4. Los datos se escriben en la hoja de “Inscripciones a webinars” en Sheets.  

---

## 2. Conexión con systeme.io (extracción de datos)  

- systeme.io fue la fuente de la mayoría de los datos operativos (leads, compras, registros de charlas y webinars).  
- Como systeme.io no ofrecía integraciones API completas en esta etapa, se optó por **usar sus correos automáticos** como **gateway de información**.  
- Gmail recibió estos correos → Make y Apps Script se encargaron de la transformación y registro en Sheets.  

---

## 3. Conexión con Google Drive (almacenamiento de CVs)  

- Los postulantes cargaban sus CVs en formularios que derivaban a Google Drive.  
- Drive se integró tanto con Sheets como con Make:  
  - **Sheets:** almacenaba enlaces directos a los archivos.  
  - **Make:** descargaba los CVs y, mediante PDF.co u otros servicios, convertía los archivos a texto para análisis automatizado.  

---

## 4. Mapa de conexiones en el ecosistema Google  

El ecosistema de Google operó de forma integrada de la siguiente manera:  

- **Gmail**  
  ↔ systeme.io (entrada de correos automáticos)  
  ↔ Make (disparador de flujos de automatización)  

- **Google Sheets**  
  ↔ Make (carga automática de leads, clientes, inscriptos)  
  ↔ Apps Script (automatizaciones on-demand, envío de correos masivos)  
  ↔ Landing page (uso como backend ligero para vacantes)  

- **Google Drive**  
  ↔ Formularios (almacenamiento de documentos)  
  ↔ Make (procesamiento y conversión de archivos)  

📷 **Mapa visual de integraciones**  
![Mapa de integraciones](./img/Mapa%20integraciones.png)  

---

## Conclusión  

Gracias a estas integraciones, **Google Workspace se transformó en el núcleo operativo** del proyecto, combinando:  
- **Recolección de datos** (vía Gmail + systeme.io).  
- **Procesamiento y orquestación** (con Make).  
- **Almacenamiento estructurado** (Sheets + Drive).  
- **Automatización personalizada** (Apps Script).  

Este esquema permitió una gestión eficiente y flexible de leads, clientes y postulantes, asegurando trazabilidad y escalabilidad en las operaciones de UPLIN HR.  
