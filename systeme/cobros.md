# Systeme.io – Cobros  

Este documento describe la integración y funcionamiento de los **métodos de cobro en systeme.io**, con especial foco en las pasarelas de **PayPal** y **Mercado Pago**, junto con las limitaciones detectadas durante su implementación.  

---

## 1. Integración de PayPal  

- **Requisito de cuenta empresa:** fue necesario convertir la cuenta titular en una **cuenta empresarial de PayPal**.  
- **Limitación para el pagador:** el usuario debía **loguearse obligatoriamente en PayPal** para poder completar el pago.  
  - Una vez logueado, podía elegir pagar con su tarjeta de crédito o débito.  
  - Sin embargo, no existía la opción de ingresar directamente los datos de tarjeta sin cuenta PayPal, lo cual generó fricción en algunos usuarios.  
- **Gestión de facturación:** PayPal exige la **subida de facturas** en la cuenta, incluso en casos donde legalmente puede existir exención (ej. regímenes de autónomos o monotributo).  

> 📄 Documentación oficial: [Integración de PayPal con systeme.io](https://help-es.systeme.io/article/657-integracion-de-paypal)  

---

## 2. Integración de Mercado Pago  

- **Ventaja principal:** no requiere logueo del usuario, permitiendo ingresar directamente los datos de tarjeta.  
- **Limitaciones:**  
  - No admite cobros en **moneda dólar**, únicamente en moneda local.  
  - Su uso está restringido a **algunos países de Latinoamérica**, lo que reduce la escalabilidad global.  

> 📄 Documentación oficial: [Integración de Mercado Pago con systeme.io](https://help-es.systeme.io/article/2090-como-integrar-tu-cuenta-de-mercado-pago-a-systeme-io)  

---

## 3. Limitaciones generales detectadas  

Además de las particularidades de cada pasarela, se identificaron fallas y limitaciones críticas en el sistema de cobros de systeme.io:  

1. **Moneda única en túneles de venta**  
   - Cada túnel sólo admite una moneda de cobro.  
   - Para vender en distintas monedas es necesario **duplicar los túneles** y configurarlos por separado.  

2. **Cobros recurrentes sin actualización de precio**  
   - En membresías o suscripciones, el sistema **mantiene fijo el precio inicial**.  
   - No admite actualización del valor, ni siquiera en contextos de inflación o monedas fluctuantes.  

3. **Monto máximo de cobro**  
   - El límite es de **1.000.000 (sin impuestos)**, independiente de la moneda.  
   - Esto implica que tanto en **dólares** como en **pesos argentinos** el tope es el mismo.  
   - Ejemplo crítico: a la fecha de este documento, **1.000.000 ARS ≈ 667 USD**, lo que imposibilita ciertos cobros locales.  

4. **Ausencia de conversión automática de moneda**  
   - Si se establecen precios en dólares, systeme.io no los traduce a otra moneda.  
   - Es inferior a plataformas más avanzadas que permiten cotizaciones dinámicas.  

5. **Restricciones adicionales de PayPal**  
   - Obliga a utilizar cuenta empresarial incluso en casos donde legalmente no sería necesario.  
   - Este requisito entorpece la operativa y eleva la carga administrativa.  

---

## 4. Otras limitaciones observadas  

- **Soporte limitado:** los tiempos de respuesta del soporte técnico de systeme.io para problemas de cobro no siempre fueron adecuados frente a la criticidad de los errores.  
- **Integraciones externas restringidas:** a diferencia de otras pasarelas, no hay APIs extendidas que permitan ampliar la lógica de cobros más allá de lo básico.  
- **Experiencia del usuario final:** la obligatoriedad de múltiples pasos (ej. logueo en PayPal) afecta la tasa de conversión.  

---

**Conclusión:**  
El sistema de cobros de systeme.io permitió implementar ventas internacionales y regionales, pero presentó **limitaciones significativas** que impactaron en la experiencia de usuarios y en la escalabilidad del proyecto. En particular, los problemas con **monedas, actualizaciones de precios y montos máximos** fueron críticos y requieren soluciones externas o la consideración de plataformas complementarias en fases futuras.  

---
