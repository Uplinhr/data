# Systeme.io – Automatizaciones  

Este archivo describe las **automatizaciones implementadas en systeme.io**, orientadas a la comunicación con leads y clientes, la gestión de campañas y el manejo eficiente de procesos internos.  

---

## 1. Secuencias de correos  

Se configuraron diferentes **secuencias automáticas de correos** para acompañar a los leads y clientes en distintas etapas del embudo:  
- **Correos de bienvenida** para nuevos registros.  
- **Recordatorios** previos a webinars o eventos.  
- **Seguimiento** luego de la inscripción a un servicio o curso.  
- **Mensajes de fidelización** para clientes activos.  

Estas secuencias se disparaban en función de acciones específicas (registro en un formulario, compra de un servicio, apertura de un correo previo, etc.).  

**Objetivo:** garantizar una comunicación rápida, personalizada y sin intervención manual.  

---

## 2. Campañas y newsletters  

- Se implementaron **campañas de email marketing** dirigidas a distintos segmentos de leads.  
- Se enviaron **newsletters periódicas** con novedades de UPLIN HR, promociones y próximos webinars.  
- El sistema permitió medir **tasas de apertura y clics**, lo cual ayudó a optimizar la estrategia de comunicación.  

**Objetivo:** mantener informados a los leads y clientes, fomentando la participación y la conversión.  

---

## 3. Segmentación y etiquetado  

- Los contactos fueron organizados mediante **etiquetas** en función de su comportamiento:  
  - Registro en webinars.  
  - Compra de un curso o servicio.  
  - Interacciones con correos (apertura, clics).  
- Esta segmentación permitió **personalizar los envíos**, dirigiendo cada campaña al público más relevante.  

**Objetivo:** aumentar la eficacia de las comunicaciones y evitar envíos masivos poco personalizados.  

---

## 4. Automatizaciones configuradas  

En total se configuraron alrededor de **100 automatizaciones activas en systeme.io**, muchas de ellas basadas en estructuras repetitivas de disparadores y acciones.  
Estas automatizaciones cubrieron procesos clave como la **venta de servicios**, la **suscripción a charlas grabadas**, la **compra de cursos pagos o certificaciones**, la **captación de leads** y la **gestión de formularios de contacto**.  

Cada flujo consistió en **triggers predefinidos** (ej. nueva venta, error de pago, suscripción a un formulario) que disparaban acciones automáticas como **asignación o eliminación de etiquetas**, **envío de correos automáticos** y **notificaciones a direcciones de correo específicas**.  

Este diseño permitió mantener la trazabilidad de las interacciones, optimizar la comunicación con los leads y garantizar la actualización constante del CRM.  

### Flujos automatizados (general)  

| Nombre de la automatización | Disparador | Acciones principales |
|-----------------------------|------------|----------------------|
| **Venta de servicios – Nueva venta** | Venta nueva | Asignar etiqueta · Enviar correo al cliente · Enviar correo a dirección específica |
| **Venta de servicios – Cancelación** | Venta cancelada / devolución / cancelación de suscripción | Eliminar etiqueta · Asignar nueva etiqueta · Enviar correo al cliente · Enviar correo a dirección específica |
| **Venta de servicios – Error de pago** | Error en el pago | Enviar correo al cliente · Enviar correo a dirección específica |
| **Suscripción a charlas grabadas** | Suscripción a formulario de etapa de túnel | Asignar etiqueta · Enviar correo al cliente · Enviar correo a dirección específica |
| **Compra de cursos pagos/certificaciones – Nueva venta** | Venta nueva | Asignar etiqueta (doble asignación según categoría del curso) |
| **Compra de cursos pagos/certificaciones – Cancelación** | Venta cancelada | Eliminar etiqueta · Asignar nueva etiqueta · Enviar correo al cliente · Enviar correo a dirección específica |
| **Compra de cursos pagos/certificaciones – Error de pago** | Error en el pago | Enviar correo al cliente · Enviar correo a dirección específica |
| **Captación de leads** | Suscripción a formulario previo a etapa de compra | Asignar etiqueta · Enviar correo al cliente · Enviar correo a dirección específica |
| **Formulario de contacto** | Suscripción a formulario de contacto | Asignar etiqueta · Enviar correo al cliente · Enviar correo a dirección específica |

---
