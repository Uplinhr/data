# Systeme.io – Limitaciones  

Este documento detalla las principales **restricciones detectadas en systeme.io**, los motivos que llevaron a complementar la plataforma con otras herramientas, y las posibles alternativas a considerar en el futuro.  

---

## 1. Restricciones de systeme.io  

Durante la implementación del proyecto se identificaron limitaciones importantes:  

- **Integración incompleta vía API**: la plataforma no permite una integración plena con APIs externas, lo cual restringe su capacidad de conectarse con otros sistemas de manera avanzada.  
- **CRM básico**: el módulo de CRM es limitado, sin vistas avanzadas, filtros complejos ni parametrización profunda de los contactos.  
- **Gestión de cursos**: ofrece herramientas muy restringidas para la administración de cursos pagos, sin funcionalidades robustas de seguimiento ni certificaciones automatizadas.  
- **Webinars en vivo**: no permite la transmisión directa dentro de la plataforma; únicamente gestiona inscripciones y accesos a enlaces externos.  
- **Diseño limitado y obsoleto**:  
  - El editor nativo tiene poca flexibilidad.  
  - Como ventaja, admite mejoras mediante la incorporación manual de **HTML, CSS y JavaScript** en elementos individuales.  
- **Sistema de correos poco amigable**:  
  - El editor de correos no es compatible con estándares modernos de edición de texto.  
  - La experiencia de creación y personalización es inferior a la de herramientas especializadas.  
  - Como ventaja, posee una política estricta contra spam, lo que protege la reputación de envío.  
- **Cobros restringidos**:  
  - Límite máximo de **1 millón** en cobros, independiente de la moneda (ej. 1 millón de pesos argentinos = 667 USD al momento de la redacción).  
  - Aplicable tanto a productos físicos como digitales.  
- **Ausencia de carrito de compras**:  
  - No permite que un usuario agregue varios productos y los pague en una única transacción.  
  - Solo admite upsells y downsells tras una compra.  
- **Información incompleta al administrador**:  
  - Las notificaciones de compra no incluyen parámetros de personalización suficientes.  
  - No se detalla de manera clara qué producto o servicio fue adquirido.  
- **Planes y costos**: la relación **precio – calidad** de los planes es cuestionable frente a otras plataformas más flexibles.  

---

## 2. Razones para integrar Make y Google Workspace  

Debido a estas limitaciones, fue necesario complementar systeme.io con otras herramientas:  

- **Make (Integromat)**:  
  - Permite crear flujos de automatización avanzados que systeme no cubre.  
  - Facilita la **extracción de datos**, la transformación (ETL) y la integración con bases externas.  

- **Google Workspace (Sheets, Gmail, AppScripts)**:  
  - Ofrece un **entorno más flexible** para la gestión de datos y la creación de procesos simples con botones o macros.  
  - Permite a los clientes acceder a tablas dinámicas, dashboards y reportes sin depender del CRM básico de systeme.io.  
  - Gmail sirvió como soporte adicional para comunicaciones y backup de correos.  

**En síntesis:** Make y Google Workspace se adoptaron como **extensiones necesarias** para compensar las limitaciones de systeme.io en escalabilidad, personalización y control de datos.  

---

## 3. Posibles alternativas futuras  

Para superar las limitaciones de systeme.io, se pueden considerar alternativas más robustas y escalables:  

- **Plataformas LMS especializadas** (ej. Moodle, Thinkific, Teachable) para una mejor gestión de cursos y certificaciones.  
- **Herramientas de email marketing avanzadas** (ej. Mailchimp, ActiveCampaign) que permitan segmentación compleja y editores modernos.  
- **Soluciones de e-commerce completas** (ej. Shopify, WooCommerce) para habilitar carritos de compras, múltiples monedas y mejores integraciones de pago.  
- **CRM profesionales** (ej. HubSpot, Zoho, Pipedrive) que ofrezcan mayor personalización y reportes avanzados.  
- **Pasarelas de pago con APIs abiertas** (ej. Stripe) que brinden mayor flexibilidad en la configuración de cobros, suscripciones y facturación.  

---

**Conclusión:**  
Systeme.io resultó una herramienta útil para centralizar los procesos iniciales de marketing, ventas y comunicaciones en UPLIN HR. Sin embargo, sus limitaciones estructurales obligaron a integrar herramientas externas para garantizar escalabilidad, flexibilidad y control de la información.  
A futuro, se recomienda considerar plataformas complementarias o alternativas que permitan superar las restricciones mencionadas y mejorar la experiencia tanto para los administradores como para los usuarios finales.  

---
