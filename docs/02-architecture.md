# Arquitectura del sistema (MVP)

## Visión general

UptimeGuard se compone de tres piezas principales:

1. **API Service**: expone endpoints REST para gestionar checks y consultar estados/resultados.
2. **Worker Scheduler**: ejecuta checks periódicos, registra resultados y dispara alertas cuando cambia el estado.
3. **Database**: almacena configuración de checks, estado actual e historial de ejecuciones.

Esta separación está diseñada para reflejar un patrón común en sistemas reales: una API (interfaz) y un proceso asíncrono (worker) que hace el trabajo recurrente.

---

## Diagrama de componentes

```mermaid
flowchart LR
  U[Usuario] -->|REST API| A[API Service]
  A --> DB[(Database)]

  W[Worker Scheduler] --> DB
  W -->|HTTP request| T[Target URL]
  W -->|Webhook| D[Discord]
