# Limitaciones de Make  

Este documento recopila las principales restricciones encontradas al utilizar **Make** en el proyecto de UPLIN HR, y explica por qué en algunos casos fue necesario complementar los flujos con otras herramientas (ej. Google AppScripts).  

---

## 1. Restricciones técnicas  

- **Límites de operaciones según plan contratado**  
  - La ejecución de escenarios depende de la cantidad de operaciones mensuales incluidas en el plan.  
  - En picos de tráfico o múltiples automatizaciones simultáneas, se alcanzaba rápidamente el límite.  

- **Velocidad de ejecución**  
  - Algunos escenarios de alto volumen (ej. inscripciones masivas a webinars) podían retrasarse.  
  - Los “delays” en la ejecución no eran óptimos para procesos que requerían inmediatez.  

- **Parseo de correos**  
  - El análisis de correos con JSON incrustado a veces fallaba por formatos irregulares.  
  - Fue necesario complementar con reglas regex o IA para asegurar la limpieza de datos.  

- **Manejo de archivos**  
  - Si bien Make puede integrarse con Google Drive, los procesos de conversión de PDF a texto dependían de integraciones externas (ej. PDF.co).  
  - El manejo de archivos adjuntos grandes (más de 10 MB) resultaba poco confiable.  

---

## 2. Limitaciones de diseño de escenarios  

- **Complejidad visual**  
  - Los escenarios con más de 10 módulos se volvían difíciles de leer y mantener.  
  - Requerían documentarse aparte para no perder trazabilidad.  

- **Falta de control de versiones**  
  - Make no posee un sistema nativo para versionar escenarios.  
  - Cada modificación debía documentarse manualmente.  

- **Errores silenciosos**  
  - En ocasiones, Make no notificaba fallos menores en transformaciones de datos.  
  - Esto generaba inconsistencias que solo se detectaban en la base final.  

---

## 3. Razones para complementar con Google AppScripts  

- **Mayor control en transformaciones personalizadas**  
  - AppScripts permitió diseñar rutinas específicas (ej. correcciones manuales de registros, botones en Sheets para procesos on-demand).  

- **Flexibilidad en validaciones**  
  - Validaciones avanzadas (ej. condiciones según provincia, tipo de cliente o plan adquirido) fueron más fáciles de implementar directamente en AppScripts.  

- **Integración directa con el ecosistema de Google**  
  - AppScripts trabaja de manera nativa sobre Google Sheets, sin límites de operaciones mensuales.  
  - Esto permitió usar Make para orquestar y AppScripts para ajustes finos.  

---

## 4. Posibles mejoras futuras  

Para superar estas limitaciones, se recomienda:  

- Considerar **plataformas complementarias** (ej. n8n, Zapier, Airbyte) con mayor flexibilidad o licenciamiento distinto.  
- Implementar **pipelines ETL dedicados** en entornos de datos (ej. BigQuery + Dataflow, Airflow).  
- Diseñar un esquema de **monitorización** (logs automáticos de errores, dashboards de ejecución).  
- Documentar cada escenario con capturas y JSON exportado para suplir la falta de control de versiones nativo.  

---

**Conclusión:**  
Make resultó fundamental para conectar systeme.io con Google Workspace y bases de datos, pero su dependencia de límites de operaciones, fallos en parseos complejos y falta de versionado hicieron necesario apoyarse en herramientas adicionales como AppScripts para lograr estabilidad y control total.  

---
