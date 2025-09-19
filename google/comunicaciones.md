# Comunicaciones con Gmail  

El ecosistema de **Google Workspace** fue clave para gestionar comunicaciones internas y externas. En particular, **Gmail** se utilizó como canal principal de notificación y respaldo de correos automáticos generados por systeme.io.  

---

## 1. Uso de Gmail en el proyecto  

- **Notificaciones internas:**  
  Cada vez que se producía un evento relevante (ej. nueva inscripción, compra o cancelación), systeme.io enviaba un correo que quedaba registrado en la casilla de Gmail de UPLIN HR.  
  Esto aseguraba un **respaldo adicional** de la información y facilitaba la verificación manual.  

- **Respaldo de correos automáticos:**  
  Todos los mensajes enviados a leads y clientes quedaban replicados en Gmail, funcionando como un **log natural** de las interacciones realizadas desde systeme.io.  

---

## 2. Integración con Make  

- Gmail fue la **puerta de entrada** de muchos flujos en Make.  
- Los correos recibidos desde systeme.io eran analizados con **Regex** o directamente con el módulo **Parse JSON** para extraer datos estructurados (nombre, correo, país, producto adquirido).  
- Estos datos se derivaban luego a Google Sheets o a bases de datos SQL para su almacenamiento y análisis.  

Ejemplo de flujo:  
1. Gmail recibe correo de confirmación de compra.  
2. Make detecta el correo entrante → extrae contenido.  
3. Los datos se normalizan (regex, JSON parse).  
4. Se cargan automáticamente en la tabla de clientes B2B.  

---

## 3. Plantillas de correos internos  

Se diseñaron **plantillas simples** en Gmail para responder rápidamente a situaciones frecuentes:  
- Confirmación manual de inscripción a webinars.  
- Notificación de seguimiento a leads B2B.  
- Respuesta automática en casos de soporte o consultas por formulario de contacto.  

Estas plantillas incluían atributos de personalización (ej. nombre de pila, apellido) para mantener un trato más humano y cercano.  

---

## 4. Integración con Apps Script  

Gmail también fue potenciado mediante **Google Apps Script**, que permitió el envío de correos masivos y automatizados **on-demand**, directamente desde Google Sheets.  

Ejemplos:  
- **Correo de bienvenida:** enviado a postulantes que cargaban su CV en la base de datos.  
- **Correo de rechazo:** notificación masiva a candidatos no seleccionados, directamente desde la hoja de vacantes.  
- **Campañas personalizadas:** respuestas rápidas al hacer clic en botones dentro de Sheets.  

Estos procesos se integraron con los datos ya organizados en Sheets, reduciendo errores y acelerando la comunicación.  

---

## Conclusión  

El uso de **Gmail como hub de comunicaciones**, integrado con Make y Apps Script, permitió:  
- Mantener un registro completo y confiable de correos enviados.  
- Disparar automatizaciones a partir de correos recibidos.  
- Ejecutar envíos masivos y personalizados desde Sheets, sin depender de plataformas externas.  

En conjunto, Gmail funcionó como **núcleo operativo de la comunicación digital** en el proyecto UPLIN HR.  
