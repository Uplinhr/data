# Limitaciones en Data & Analytics  

El plan de analítica de UPLIN HR se diseñó para crecer progresivamente, pero durante la implementación inicial se identificaron ciertas **restricciones técnicas y operativas** en el uso de GA4 y Looker Studio.  

---

## 1. Limitaciones en GA4  

- **Curva de aprendizaje**  
  - La interfaz de Google Analytics 4 es más compleja que la de Universal Analytics.  
  - Requirió tiempo de configuración para eventos personalizados y filtros adecuados.  

- **Muestreo de datos**  
  - En reportes con gran volumen de tráfico, GA4 puede aplicar muestreo, reduciendo precisión.  

- **Anonimato de usuarios**  
  - GA4 no permite identificar usuarios individuales de forma directa, lo que limita el cruce con información de CRM sin integraciones adicionales.  

- **Eventos personalizados**  
  - La configuración inicial de eventos requiere codificación específica en GTM o ajustes manuales en systeme.io.  

---

## 2. Limitaciones en Looker Studio  

- **Dependencia de GA4 como fuente única**  
  - Los dashboards iniciales dependieron exclusivamente de GA4, lo que limitó el cruce con otras fuentes (ej. SQL, CRM).  

- **Performance**  
  - Los tableros con filtros dinámicos y periodos largos de tiempo tardaban en cargar.  

- **Seguridad y permisos**  
  - El manejo de accesos compartidos no siempre se adapta a las necesidades de privacidad de datos sensibles.  

---

## 3. Limitaciones estructurales  

- **Falta de un data warehouse**  
  - No se implementó en la primera fase un repositorio robusto como BigQuery.  
  - Esto impidió mantener histórico detallado de eventos y realizar análisis avanzados (ej. cohortes, segmentación profunda).  

- **Escalabilidad**  
  - Al depender de GA4 + Looker Studio, la escalabilidad se ve restringida para grandes volúmenes de datos.  
  - Se vuelve necesario contemplar migración futura a soluciones más robustas (ej. BigQuery, Microsoft Fabric, Power BI).  

---

## 4. Justificación de la combinación con otras herramientas  

- **CRM**: necesario para enriquecer datos de GA4 con información de usuarios reales (empresas, compras, formularios).  
- **Google Sheets**: funcionó como backend ligero para centralizar datos pequeños o de origen manual.  
- **Make**: facilitó la extracción y transformación de datos hacia formatos compatibles con el ecosistema de Google.  

---

## Conclusión  

GA4 y Looker Studio fueron **suficientes para el MVP**, pero mostraron limitaciones en escalabilidad, personalización y análisis avanzado.  
Esto refuerza la necesidad de avanzar hacia un **data warehouse** (BigQuery o equivalente) y un CRM integrado, garantizando un ecosistema más sólido y escalable para el futuro de UPLIN HR.  

---
