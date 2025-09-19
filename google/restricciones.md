# Limitaciones de Google Workspace  

Aunque **Google Workspace** fue clave para centralizar y organizar la información en UPLIN HR, se identificaron varias **restricciones técnicas y operativas** que motivaron la integración con herramientas externas como Make y Apps Script.  

---

## 1. Restricciones en Google Sheets  

- **Límite de filas y celdas**  
  - Al superar ciertos volúmenes de datos (cientos de miles de filas), las hojas se volvían lentas e inestables.  
  - Esto afectaba especialmente a tablas de inscripciones masivas a webinars y charlas.  

- **Problemas de performance**  
  - Fórmulas complejas (ej. `ARRAYFORMULA` con múltiples transformaciones) impactaban en el tiempo de carga.  
  - El refresco de datos en tiempo real se volvía poco práctico cuando había muchos usuarios consultando la hoja.  

- **Ausencia de funciones propias de bases de datos**  
  - Faltaban consultas avanzadas y normalización nativa, lo que obligaba a trasladar parte de la información a SQL.  

---

## 2. Restricciones en Gmail  

- **Límites de envío**  
  - Gmail tiene un límite diario de correos enviados (aprox. 500 en cuentas estándar y 2.000 en cuentas de Workspace).  
  - Esto impidió usarlo como herramienta de email marketing a gran escala.  

- **Restricciones para campañas**  
  - No se adapta bien a la lógica de campañas o newsletters recurrentes.  
  - Fue necesario mantener systeme.io como plataforma principal de envíos masivos.  

---

## 3. Restricciones en Google Drive  

- **Gestión de archivos pesados**  
  - Archivos de CVs muy grandes (superiores a 10 MB) generaban problemas en la integración con Make.  
  - La conversión a texto dependía de servicios externos (ej. PDF.co), ya que Drive no ofrece OCR ni extracción avanzada de datos por sí mismo.  

- **Estructura de carpetas**  
  - La falta de un sistema avanzado de versionado y categorización dificultaba el manejo de grandes volúmenes de documentos.  

---

## 4. Justificación de la combinación con otras herramientas  

Debido a estas limitaciones, Google Workspace se utilizó en conjunto con:  

- **Make**: para orquestar flujos, extraer datos de correos, y distribuirlos en Sheets y SQL.  
- **Apps Script**: para agregar funciones personalizadas y superar las limitaciones de Gmail y Sheets (ej. envío de correos masivos, validaciones on-demand).  
- **Systeme.io**: para campañas de email marketing y gestión CRM básica.  
- **Bases SQL externas**: para un almacenamiento más robusto y escalable.  

---

## Conclusión  

Google Workspace fue el **núcleo operativo** del proyecto, pero no suficiente por sí solo. Las limitaciones de escala, performance y envíos masivos lo convirtieron en una **pieza central del ecosistema**, complementada por Make, Apps Script, systeme.io y bases de datos SQL.  

---
