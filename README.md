# Project Documentation 
## Data - Systeme - Automation  


<p align="center">
  <img src="./make/img/banner%20readme%20data.jpeg" alt="Banner Data" />
</p>


Este proyecto fue desarrollado para **UPLIN HR**, con el objetivo de implementar una **landing page** con funcionalidades orientadas tanto a clientes (*customers*) como a empresas (*business*).  

En una primera etapa, dichas funcionalidades se construyeron mediante **integraciones básicas entre plataformas**, complementadas luego con la incorporación de un **software de gestión empresarial**.  

El equipo de trabajo estuvo conformado por:  
- [**Gastón Orellano**](https://www.linkedin.com/in/gaston-orellano/) – Data, Automation & Business Analyst (coordinación general)  
- [**Tomás Ulman**](https://www.linkedin.com/in/tomasagustinulman/) – Frontend Lead & UX  
- [**Marco Pistagnesi**](https://www.linkedin.com/in/marco-pistagnesi-0a3993243/) – Backend & Database Lead  
- [**Andrea Palacios**]() – UI Designer  

El proyecto integral se puede consultar en el **repositorio principal de Uplinhr en GitHub**.  
- El desarrollo backend del Software de Gestión y Base de Datos se encuentra en: [https://github.com/Uplinhr/backend](https://github.com/Uplinhr/backend)  
- El desarrollo frontend (que comprende tanto el Software de Gestión como la Landing Page y todas sus funcionalidades) se encuentra en: [https://github.com/Uplinhr/uplinhr](https://github.com/Uplinhr/uplinhr)  

---

## Índice de contenidos  

1. [Systeme.io](#1-systemeio)  
2. [Make (Integromat)](#2-make-integromat)  
3. [Google Workspace](#3-google-workspace)  
4. [Data Management](#4-data-management)  
5. [Métricas y Analítica](#5-métricas-y-analítica)
6. [Estructura del repositorio](#6-estructura-del-repositorio)    

---

## 1. Systeme.io  

Systeme.io fue la **plataforma central** para la construcción inicial del proyecto.  
Se implementaron las siguientes funcionalidades:  
- **Landing page** en dominio propio y subdominio para systeme.  
- **Pasarelas de pago** integradas: PayPal y Mercado Pago (según disponibilidad por país).  
- **Túneles de venta** para servicios y cursos pagos.  
- **Túneles de captación** con formularios de leads.  
- **Túneles de webinar** para inscripciones en charlas en vivo y grabadas.  
- **Automatizaciones internas**: envío de correos, gestión de leads en el CRM y campañas de email marketing (newsletters y secuencias).  
- **Conexiones externas** mediante webhooks y correos automatizados para enviar información a otras plataformas.  

**Objetivo:** centralizar en una sola plataforma el marketing digital, la captación de clientes y la venta de servicios, asegurando rapidez de implementación y escalabilidad básica.  

---

## 2. Hubspot 

Hubspot.com fue la **Plataforma de Migracion**, se Transfirieron los datos y servicios que se utilizaban en systeme a hubspot. Solamente quedo activo en systeme la pasarela de pago en ARS y USD del curso People analytics y la inscripcion a Uplin community (La inscripcion se automatiza con make y se pasa a la hoja de calculo).
Se implementaron las siguientes funcionalidades:
- **Captacion de leads** mediante la creacion de contactos.
- **Correos de seguimiento y newsletters** para comentarles a los leads nuestros proyectos e informacion relevante.
- **Cursos pagos por correo**: Se envio correos de modulos grabados a los estudiantes de dichos cursos.
- **Túneles de captación** con formularios de leads.
- **Túneles de asesoramiento** con formularios para cada servicio que el cliente desee contratar.
- **Programador de reuniones** con la creacion de un calendario conectado al Google meet empresarial.

**Objetivo:** centralizar en una sola plataforma el marketing digital y la captación de clientes, asegurando rapidez de implementación y escalabilidad básica.  

---

## 3. Make (Integromat)  

Make se utilizó como herramienta de **automatización avanzada** y **procesamiento de datos (ETL)**.  
Se desarrollaron flujos para:  
- Extraer información de systeme a través de webhooks y correos automatizados.  
- Corregir y transformar datos antes de su almacenamiento.  
- Cargar la información en una **base de datos propia** estructurada en tablas (inscriptos, clientes, leads).  

**Objetivo:** mantener un registro centralizado y confiable de todos los procesos, complementando las capacidades limitadas del CRM de systeme.io.  

---

## 4. Google Workspace  

El ecosistema de Google Workspace fue clave para la **operación diaria y el acceso del cliente a los datos**.  
Se utilizaron:  
- **Gmail y Sheets** para comunicaciones y gestión de información.  
- **AppScripts** para automatizaciones directas en Google Sheets, ejecutables con un botón, que permitieron al cliente realizar procesos sin conocimientos técnicos.  
- **Sheets como backend ligero** para publicar tarjetas informativas en la landing page, evitando redeploys técnicos.  

**Objetivo:** ofrecer a UPLIN HR herramientas simples, familiares y de bajo costo para manejar información en tiempo real.  

---

## 5. Data Management  

Se diseñó un esquema de **tablas organizadas** que permitió a UPLIN HR acceder en forma inmediata a:  
- Inscripciones a webinars.  
- Clientes activos.  
- Leads interesados en servicios.  

Además, se integraron estas tablas con los flujos de Make para asegurar datos consistentes y listos para análisis.  

**Objetivo:** brindar trazabilidad y control total sobre la información generada por las diferentes automatizaciones y plataformas.  

---

## 6. Métricas y Analítica  

Para el seguimiento del desempeño digital se implementaron:  
- **Google Tag Manager (GTM)** para recolectar información de visitas a la landing page.  
- **Google Analytics 4 (GA4)** para medir interacciones, conversiones y comportamiento de usuarios.  
- Migración de los dashboards a **Google Looker Studio** para personalización avanzada y reporting.  

**Objetivo:** monitorear el tráfico, evaluar el rendimiento de los túneles y tomar decisiones basadas en datos sobre marketing y captación de clientes.  


## 7. Estructura del repositorio  

El repositorio está organizado en secciones que reflejan los diferentes componentes del proyecto:  

uplinhr-automation-docs/ <br>
│<br>
├── systeme/ # Documentación de integraciones en systeme.io <br>
├── hubspot/ # Procesos de Captacio de leads en Hubspot.com <br> 
├── make/ # Flujos y procesos de automatización con Make <br>
├── google/ # Uso de Google Workspace (Sheets, AppScripts, Gmail) <br>
├── data/ # Gestión de bases de datos, métricas y dashboards <br>
└── README.md # Documento principal del proyecto <br>


