# Transformación Digital e Hiperautomatización en Hospital Obarrio
### Enfoque de Gestión por Procesos aplicado a la Gestión de Turnos Odontológicos

**Universidad Tecnológica Nacional — Facultad Regional Tucumán (UTN-FRT)**  
**Cátedra:** Gestión de Procesos de Negocio (GPN)  
**Proyecto:** Trabajo Final Integrador  

---

## 📌 Resumen del Proyecto

El presente proyecto aborda la problemática del proceso de asignación y gestión de turnos odontológicos en el **Hospital Obarrio** (Tucumán, Argentina). 

### 🔴 Situación Actual (Proceso AS-IS)
* **Atención presencial/telefónica:** Lunes a Viernes de 07:00 a 19:00 hs.
* **Procesos manuales:** Carga manual de datos, verificación en ficheros y planillas locales.
* **Inconvenientes:** Tiempos prolongados de espera (10–15 min por solicitud), alta probabilidad de error humano, falta de trazabilidad y nula información en tiempo real.

### 🟢 Solución Rediseñada (Proceso TO-BE)
* **Canal conversacional 24/7:** Bot de Telegram impulsado por un Agente de IA (`gpt-4.1-mini` con *function calling*).
* **Autogestión total:** Solicitud, confirmación, verificación y cancelación de turnos de manera autogestionada (<2 min).
* **Trazabilidad y Métricas:** Sincronización automática de datos entre Google Calendar, Supabase y Google Sheets, notificando confirmaciones/cancelaciones por Gmail y procesando encuestas de satisfacción post-atención mediante un LLM.

## 🛠️ Arquitectura e Integraciones

El sistema de automatización fue construido sobre la plataforma low-code **n8n** y se compone de tres flujos de trabajo coordinados:

1. **`main-flow.json` (Agente de IA Orquestador):**
   * **Trigger:** Telegram Bot Webhook.
   * **Motor:** LangChain + OpenAI (`gpt-4.1-mini`).
   * **Herramientas (Tools):** `consultar_turnos` (Supabase), `crear_evento_calendario` (Google Calendar), `buscar_mis_turnos_activos`, `borrar_evento` y `actualizar_disponibilidad_supabase` (Subworkflow).
2. **`subworkflow-db-sync.json` (Sincronizador y Notificador):**
   * Recalcula la disponibilidad horaria mediante un nodo de código JavaScript.
   * Sincroniza la tabla de cupos en Supabase.
   * Almacena el historial en Google Sheets.
   * Envía emails dinámicos HTML de confirmación o cancelación vía Gmail.
3. **`subworkflow-satisfaction-survey.json` (Encuesta y Análisis de Sentimiento):**
   * Captura el feedback recibido por el paciente desde un Formulario Web de n8n.
   * Utiliza una cadena LLM para clasificar el **sentimiento** (Positivo, Neutral, Negativo) y la **categoría del servicio**.
   * Almacena los resultados procesados en Google Sheets para su posterior consumo en Power BI.

### 🔌 Integraciones Tecnológicas
| Sistema Externo | Tipo de Integración | Propósito |
| :--- | :--- | :--- |
| **Telegram API** | Webhook / Bot API | Canal conversacional interactivo con el paciente |
| **OpenAI (GPT-4.1-mini)** | API REST | Agente conversacional (*function-calling*) y análisis de sentimiento |
| **n8n** | Self-Hosted / Cloud | Motor orquestador de workflows e hiperautomatización |
| **Google Calendar** | OAuth2 / REST API | Gestión operativa de la agenda odontológica |
| **Supabase** | REST API / Database | Base de datos de disponibilidad de turnos en tiempo real |
| **Google Sheets** | OAuth2 API | Almacenamiento maestro de datos y BI Analytics |
| **Gmail** | OAuth2 API | Despacho de notificaciones y comprobantes HTML al paciente |
| **Power BI** | Native Connector | Visualización e inteligencia de negocios |

## 📄 Documentación del Proyecto

El documento explicativo completo con la investigación, el modelado AS-IS/TO-BE y la justificación técnica de la solución se encuentra disponible para su descarga en la sección de **Releases** del repositorio:

* 📥 **[Descargar Documento TFI (PDF)](https://github.com/micael305/n8n-medical-appointment-bot/releases/tag/v1.0.0)**

## 📁 Estructura del Repositorio

```text
.
├── workflows/
│   ├── main-flow.json                        # Flujo principal de Telegram y Agente de IA
│   ├── subworkflow-db-sync.json              # Subflujo de recálculo de agenda, DB y notificaciones
│   └── subworkflow-satisfaction-survey.json  # Subflujo de encuestas y análisis de sentimiento
└── README.md                                 # Documentación del proyecto
```

## 🚀 Guía de Despliegue en n8n

### Requisitos Previos
* Instancia de **n8n** activa (v1.0+).
* Credenciales activas para: **OpenAI API**, **Telegram Bot**, **Google Calendar**, **Google Sheets**, **Gmail OAuth2** y **Supabase**.

### Pasos de Instalación
1. Clona este repositorio:
   ```bash
   git clone [https://github.com/micael305/n8n-medical-appointment-bot.git](https://github.com/micael305/n8n-medical-appointment-bot.git)
2. Importa los archivos JSON en tu servidor de n8n:
  * workflows/subworkflow-db-sync.json
  * workflows/subworkflow-satisfaction-survey.json
  * workflows/main-flow.json
3. Vincula las credenciales correspondientes a cada nodo de n8n.
4. Ajusta el ID del flujo subworkflow-db-sync dentro de la herramienta actualizar_disponibilidad_supabase en el flujo principal.
5. Publica los tres workflows.
