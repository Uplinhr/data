# Systeme.io – CRM, Automatizaciones y Cobros  

Este apartado describe el uso de **Systeme.io** como plataforma central para la gestión de clientes, comunicaciones y procesos de venta.  

---

## 1. CRM y gestión de leads  

- Se configuró el **CRM nativo de systeme.io** para centralizar la información de contactos, leads y clientes.  
- Los leads ingresaban principalmente a través de:  
  - Formularios en túneles de captación.  
  - Registros en webinars gratuitos (en vivo o grabados).  
  - Formularios asociados a cursos pagos o servicios.  
- El CRM permitió clasificar contactos según **etapas del embudo** y registrar la interacción con cada uno.  

**Objetivo:** contar con una base de leads estructurada y accesible, sin necesidad de sistemas externos en la primera etapa.  

---

## 2. Automatizaciones internas  

Se configuraron **automatizaciones básicas** directamente en systeme.io:  
- **Gestión de Leads a través de etiquetas** para segmentar los diferentes usuarios.
- **Secuencias de correos** para leads nuevos (bienvenida, seguimiento, recordatorios).  
- **Campañas y newsletters** para mantener comunicación periódica.  
- **Etiquetas y segmentación** de leads según comportamiento (clics, apertura de mails, inscripción a webinars).  

**Objetivo:** mantener una comunicación constante con leads y clientes, reduciendo la necesidad de tareas manuales.  

---

## 3. Comunicaciones y captación  

- Se diseñaron **túneles de captación** con formularios para recolectar datos de leads.  
- Se utilizaron **túneles de webinars** para la inscripción a charlas gratuitas (en vivo y grabadas).  
- Cada inscripción generaba un registro automático en el CRM y disparaba secuencias de correos asociadas.  
- Cada inscripción también asignaba una etiqueta asociada a la gestión realizada por los leads.

**Objetivo:** facilitar la entrada de leads al CRM y garantizar un primer contacto inmediato.  

---

## 4. Cobros y pasarelas de pago  

- Se integraron las pasarelas de pago:  
  - **PayPal** para clientes internacionales.  
  - **Mercado Pago** para clientes de países con soporte en LATAM.  
- Los cobros quedaban registrados en systeme.io, vinculados automáticamente al contacto en el CRM.  
- Se habilitaron **túneles de venta** para cursos y servicios pagos.  

**Objetivo:** simplificar el proceso de compra para clientes y centralizar registros de pago en el sistema.  

---

## 5. Limitaciones detectadas  

Durante el desarrollo se identificaron algunas limitaciones de systeme.io:  
- Poca flexibilidad en la personalización de reportes.  
- Restricciones en la exportación de datos.  
- CRM básico, sin vistas avanzadas ni filtros complejos.  
- Poca adaptabilidad de las pasarelas de pago: Mercado Pago tiene limitaciones de cobro, mientras que PayPal exige logueo y cuenta del cliente para proceder con el pago.

Estas limitaciones motivaron la integración posterior con **Make** y **Google Workspace** para un manejo más robusto de la información.  

---
