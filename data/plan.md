# Plan de Data Analytics en UPLIN HR  

El enfoque analítico de UPLIN está diseñado para **evolucionar junto al proyecto**: comienza con una implementación sencilla en el MVP, pero preparada para escalar hacia soluciones más robustas a medida que se amplían los servicios y crece la base de usuarios.  

---

## 1. Conexión inicial con GA4  

Para medir el comportamiento de los usuarios en la landing page (hosteada en systeme.io), se creó una propiedad dedicada en **Google Analytics 4 (GA4)**.  

Pasos clave:  
- Se instaló el código de seguimiento en la landing.  
- Se habilitaron eventos automáticos como `page_view`, `scroll`, `click` y `form_submit`.  
- Se configuraron eventos personalizados para interacciones críticas, como:  
  - Clics en los planes disponibles.  
  - Inicio de procesos de compra.  
  - Reproducción de videos.  

Esto permitió **mapear el embudo digital** y detectar puntos de abandono o conversión en tiempo real.  

---

## 2. Visualización en Looker Studio  

Los datos recolectados en GA4 se conectaron a **Google Looker Studio** para construir tableros visuales interactivos.  

Características del tablero:  
- Dashboards dinámicos con filtros por fechas, canal de adquisición, dispositivo, tipo de plan, etc.  
- Reportes adaptados a distintos perfiles (fundadores, marketing, producto).  
- KPIs estratégicos claramente destacados para facilitar la toma de decisiones basadas en datos.  

---

## 3. Integración con CRM y personalización  

En fases intermedias del proyecto, se contempla la integración con un **CRM**, conectando datos de comportamiento (GA4) con información personal de usuarios registrados.  

Beneficios esperados:  
- Segmentar usuarios por tipo (empresa o profesional).  
- Enviar correos automáticos según acciones específicas (ej. abandono de formulario).  
- Obtener una visión unificada del cliente: navegación + interacción comercial.  

Aunque GA4 respeta el anonimato, la combinación con CRM permite conocer mejor a los clientes reales (nombre, empresa, historial de compra).  

---

## 4. Escalabilidad y análisis avanzado  

A futuro, el plan contempla herramientas de mayor capacidad analítica:  
- **Looker Studio** seguirá siendo la herramienta visual principal, pero integrando más fuentes (ej. Google Sheets, SQL).  
- **BigQuery** como motor de almacenamiento de eventos históricos y consultas SQL avanzadas.  
- Evaluación de **Microsoft Fabric / Power BI**, según necesidades del negocio.  

Recomendaciones para facilitar la transición:  
- Definir un modelo de datos normalizado (usuarios, membresías, comportamiento).  
- Usar identificadores únicos consistentes.  
- Documentar claramente los eventos personalizados y estructuras de datos.  

---

## 5. Dashboards en Looker Studio  

Una vez implementada la conexión de GA4, se construyeron tableros interactivos en **Google Looker Studio**. Estos dashboards permiten visualizar de manera clara los principales KPIs del proyecto y explorar la información con filtros dinámicos.  

### Dashboard 1 – Visión general  
Este tablero muestra la **visión global del tráfico** en la landing page:  
- Número total de usuarios y sesiones.  
- Canales de adquisición (orgánico, pago, redes sociales).  
- Distribución geográfica de los visitantes.  

📷 **Captura:**  
![Dashboard 1](./img/dashboard%201.jpg)  

---

### Dashboard 2 – Embudo de conversión  
En este dashboard se analiza el **recorrido del usuario en el embudo digital**:  
- Visualización de planes de membresía.  
- Inicio de proceso de compra.  
- Formularios completados.  
- Conversión final.  

El objetivo principal es **detectar los puntos de abandono** y mejorar la tasa de conversión.  

📷 **Captura:**  
![Dashboard 2](./img/dashboard%202.jpg)  

---

### Dashboard 3 – Eventos y comportamiento del usuario  
Este tablero se centra en los **eventos personalizados configurados en GA4**, como:  
- Clics en botones clave.  
- Reproducción de videos.  
- Scroll en secciones críticas de la landing.  

El análisis de estos eventos permite comprender **cómo interactúan los usuarios con los contenidos** y qué elementos generan mayor engagement.  

📷 **Captura:**  
![Dashboard 3](./img/dashboard%203.jpg)  

---

## 🎯 Beneficios del enfoque analítico  

- Mejora la captación y retención de usuarios.  
- Permite personalizar la experiencia del cliente.  
- Brinda una visión estratégica del rendimiento del negocio.  
- Permite escalar sin perder visibilidad sobre lo que ocurre en cada etapa.  

---

## Conclusión  

La estrategia de data en UPLIN HR fue pensada para acompañar al proyecto **desde el MVP inicial hasta su consolidación como plataforma robusta y data-driven**.  

---
