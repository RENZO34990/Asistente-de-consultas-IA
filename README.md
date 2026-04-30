# Asistente-de-consultas-IA
![Demo del proyecto](demo.gif)
Automatización de consultas con n8n, APIs y ollama

> Transforma un caso de negocio en una propuesta estratégica completa usando IA generativa — de forma automatizada y 100% local.

---

## ¿Qué hace?

Ingresás una descripción de un problema de negocio (ej: *"Empresa retail quiere mejorar ventas con IA"*) y el sistema genera automáticamente:

- ✅ **3 oportunidades estratégicas** con IA (nombre, problema que resuelve, impacto esperado)
- ✅ **1 propuesta de solución técnica** (tecnología, fases, recursos)
- ✅ **Estructura de presentación** (6+ slides listos para Gamma o PowerPoint)
- ✅ **Registro automático** en Google Sheets con fecha y link al documento
- ✅ **Google Doc generado** automáticamente con el output completo

---

## Stack

| Componente | Herramienta |
|---|---|
| Automatización | n8n (self-hosted en Docker) |
| Modelo de IA | Ollama + llama3.2 (100% local, sin costo de API) |
| Infraestructura | Docker |
| Almacenamiento | Google Sheets API |
| Documentos | Google Docs API |
| Frontend | HTML + CSS + JavaScript vanilla |

---

## Arquitectura del flujo

```
Formulario HTML
      ↓
  Webhook (n8n)
      ↓
  Set (input)
      ↓
Basic LLM Chain (Ollama llama3.2)
      ↓
Google Sheets (registro)
      ↓
Google Docs (documento generado)
```

---

## Requisitos

- Docker instalado y corriendo
- Cuenta de Google (para Sheets y Docs)
- n8n corriendo en Docker (`localhost:5678`)

---

## Instalación

### 1. Levantar Ollama en Docker

```bash
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```

Descargar el modelo:

```bash
docker exec -it ollama ollama pull llama3.2
```

### 2. Conectar Ollama y n8n en la misma red Docker

```bash
docker network create ai-network
docker network connect ai-network ollama
docker network connect ai-network TU_CONTENEDOR_N8N
```

En n8n, la URL de Ollama será:
```
http://ollama:11434
```

### 3. Importar el workflow en n8n

1. Abre n8n en `http://localhost:5678`
2. Ve a **Workflows** → **Import**
3. Sube el archivo `workflow.json` de este repositorio

### 4. Configurar credenciales de Google

En n8n ve a **Settings → Credentials** y crea una credencial `Google Sheets OAuth2 API`:

1. Crea un proyecto en [Google Cloud Console](https://console.cloud.google.com)
2. Activa **Google Sheets API** y **Google Docs API**
3. Crea credenciales OAuth2 con redirect URI: `http://localhost:5678/rest/oauth2-credential/callback`
4. Pega el Client ID y Client Secret en n8n

### 5. Crear la hoja de Google Sheets

Crea una hoja llamada `AI Consultant` con estos encabezados en la fila 1:

| Fecha | Empresa | Respuesta | Doc |
|---|---|---|---|

### 6. Abrir el formulario

Abre el archivo `ai-consultant.html` en tu navegador, pega la URL del webhook de n8n y empieza a generar propuestas.

---

## Uso

1. Selecciona la industria
2. Describe el caso de negocio
3. Pega la URL del webhook: `http://localhost:5678/webhook/ai-consultant`
4. Haz clic en **Generar propuesta estratégica**
5. El output aparece en pantalla, se guarda en Sheets y se crea un Google Doc automáticamente

---

## Archivos del repositorio

```
├── ai-consultant.html   # Formulario web (frontend)
├── workflow.json        # Flujo exportado de n8n
└── README.md            # Este archivo
```

---

## Posibles mejoras

- [ ] Conectar Gamma.app para generar slides automáticamente
- [ ] Agregar selector de modelo (llama3.2, mistral, tinyllama)
- [ ] Dashboard en Google Sheets con historial de propuestas
- [ ] Versión multiidioma del prompt

---

## Contexto

Proyecto personal desarrollado como demostración de capacidades en:
- Automatización de flujos con IA
- Integración de modelos LLM locales
- Generación de entregables de consultoría con IA generativa
- Stack AI-first sin dependencia de APIs de pago
