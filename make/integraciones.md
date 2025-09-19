# Integraciones en Make  

Este documento resume las **aplicaciones y servicios externos conectados con Make** en el proyecto de UPLIN HR. Estas integraciones funcionaron como puntos de entrada, transformación o salida de los flujos de automatización.  

---

## Lista de integraciones principales  

### 1. Systeme.io  
- **Uso:** punto de origen de la mayoría de los datos (inscripciones, compras, formularios).  
- **Conexión:** principalmente mediante correos automáticos y webhooks.  
- **Objetivo:** extraer información de leads, clientes y transacciones para procesarla en otros sistemas.  

---

### 2. Gmail  
- **Uso:** recepción de correos automáticos enviados desde systeme.io.  
- **Objetivo:** capturar datos estructurados o semiestructurados incluidos en el cuerpo de los correos.  
- **Función adicional:** notificaciones internas al equipo de UPLIN.  

---

### 3. Google Sheets  
- **Uso:** base central para el almacenamiento de datos limpios y procesados.  
- **Objetivo:** mantener registros actualizados y accesibles para el cliente en tiempo real.  
- **Función adicional:** servir como backend ligero para la publicación de información en la landing page.  

---

### 4. Bases de Datos SQL (propias)  
- **Uso:** almacenamiento estructurado y de largo plazo de leads, clientes y registros administrativos.  
- **Objetivo:** construir una base confiable y escalable para análisis y reporting.  

---

### 5. Google Drive  
- **Uso:** almacenamiento de archivos cargados por usuarios (ej. currículums).  
- **Objetivo:** integrar documentos al flujo de automatización, para posterior análisis y clasificación.  

---

### 6. PDF.co  
- **Uso:** conversión de archivos PDF (ej. currículums) a texto.  
- **Objetivo:** facilitar el análisis automático de contenido en flujos de selección y RRHH.  

---

### 7. Parse JSON  
- **Uso:** convertir bloques de texto JSON incluidos en correos en datos estructurados.  
- **Objetivo:** extraer campos específicos (nombre, correo, dirección, etc.) para luego cargar en tablas.  

---

### 8. Text Pattern  
- **Uso:** limpieza y extracción de fragmentos relevantes de texto mediante patrones regex.  
- **Objetivo:** normalizar correos y preparar datos antes del parseo JSON.  

---

## Conclusión  

Las integraciones de Make con estas aplicaciones y servicios permitieron:  
- Transformar datos provenientes de systeme.io en registros estructurados.  
- Automatizar la carga en Google Sheets y bases SQL propias.  
- Procesar documentos (ej. CVs) con herramientas externas como PDF.co.  
- Mejorar la comunicación interna con notificaciones vía Gmail.  

En conjunto, estas conexiones conformaron un **ecosistema automatizado**, escalable y adaptable a las necesidades de UPLIN HR.  

---
