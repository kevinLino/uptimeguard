# Contrato de API (MVP)

Base URL (local)
- `http://localhost:8000`

Formato:
- Request/Response en JSON
- `Content-Type: application/json`

---

## Convenciones

### Estados posibles
- `UNKNOWN`: aún no se ha ejecutado
- `UP`: responde correctamente
- `DOWN`: no responde o falla


### Modelo: Check (respuesta)
Campos principales devueltos por la API:

- `id` (string)
- `name` (string)
- `url` (string)
- `interval_seconds` (number)
- `timeout_seconds` (number)
- `enabled` (boolean)
- `last_status` (`UNKNOWN|UP|DOWN`)
- `last_checked_at` (string ISO8601 o null)
- `last_status_change_at` (string ISO8601 o null)
- `last_latency_ms` (number o null)
- `last_http_code` (number o null)
- `created_at` (string ISO8601)
- `updated_at` (string ISO8601)

---

## 1) Health & Ready

### GET /health
Devuelve si la API está viva.

**200 OK**
```json
{"status":"ok"}
```

### GET /ready
Devuelve si la API está lista (por ejemplo, puede acceder a la base de datos)

**200 OK**
```json
{"status":"ready"}
```
**503 Service Unavailable**
```json
{"status":"not_ready", "reason": "db_unreachable"}
```

## 2) Crear check

### POST /checks
Crea un nuevo check.

**Request**
```json
{
    "name":"Mi web",
    "url":"https://example.com",
    "interval_seconds":60,
    "timeout_seconds":5
}
```
**201 Created**
```json
{
    "id":"chk_123",
    "name":"Mi web",
    "url":"https://example.com",
    "interval_seconds":60,
    "timeout_seconds":5,
    "enabled":true,
    "last_status":"UNKNOWN",
    "last_checked_at":null,
    "last_status_change_at":null,
    "last_latency_ms":null,
    "last_http_code":null,
    "created_at":"2025-12-29T10:00:00Z",
    "updated_at":"2025-12-29T10:00:00Z"
}
```

**400 Bad Request (validación)**
```json
{
    "error":"invalid_request",
    "message":"interval_seconds must be >=30"
}
```

## 3) Listar checks
### GET /checks
Lista checks existentes.
**200 OK**
```json
[
    {
        "id":"chk_123",
        "name":"Mi web",
        "url":"https://example.com",
        "interval_seconds":60,
        "timeout_seconds":5,
        "enabled":true,
        "last_status":"UP",
        "last_checked_at":"2025-12-29T10:05:00Z",
        "last_status_change_at":"2025-12-29T10:05:00Z",
        "last_latency_ms":120,
        "last_http_code":200,
        "created_at":"2025-12-29T10:05:00Z",
        "updated_at":"2025-12-29T10:05:00Z"
    }
]
```

## 4) Obtener un Check
### GET /checks/{id}
Obtiene un check por id

**200 OK**
```json
{
    "id":"chk_123",
    "name":"Mi web",
    "url":"https://example.com",
    "interval_seconds": 60,
    "timeout_seconds":5,
    "enabled":true,
    "last_status":"UP",
    "last_checked_at": "2025-12-29T10:05:00Z",
    "last_status_change_at": "2025-12-29T10:05:00Z",
    "last_latency_ms": 120,
    "last_http_code": 200,
    "created_at": "2025-12-29T10:00:00Z",
    "updated_at": "2025-12-29T10:05:00Z"
}
```
**404 Not Found**
```json
{
    "error":"not_found",
    "message":"check not found"
}
```

## 5) Actualizar un check (parcial)
### PATCH /checks/{id}
Permite actualizar campos puntuales.

Campos soportados:
- name
- url
- interval_seconds(>=30)
- timeout_seconds (>=1)
- enabled (true/false)
**Request**
```json
{
    "enabled": false
}
```

**200 OK**
```json
{
  "id": "chk_123",
  "name": "Mi web",
  "url": "https://example.com",
  "interval_seconds": 60,
  "timeout_seconds": 5,
  "enabled": false,
  "last_status": "UP",
  "last_checked_at": "2025-12-29T10:05:00Z",
  "last_status_change_at": "2025-12-29T10:05:00Z",
  "last_latency_ms": 120,
  "last_http_code": 200,
  "created_at": "2025-12-29T10:00:00Z",
  "updated_at": "2025-12-29T10:06:00Z"
}
```

## 6) Eliminar un check
### DELETE /checks/{id}
Elimina un check y (en el MVP) también sus resultados asociados.

**204 No Content**
**404 Not Found**
```json
{
    "error":"not_found",
    "message":"check not found"
}
```

## 7) Estado actual (alias opcional)
### GET /checks/{id}/status
Devuelve un resumen del estado actual.

**200 OK**
```json
{
    "id":"chk_123",
    "last_status": "UP",
    "last_checked_at": "2025-12-29T10:05:00Z",
    "last_status_change_at": "2025-12-29T10:05:00Z",
    "last_latency_ms": 120,
    "last_http_code": 200
}
```

## 8) Resultados
### GET /checks/{id}/results?limit=50
Devuelve las últimas N ejecuciones (ordenadas de más reciente a más antigua).

**200 OK**
```json
[
  {
    "checked_at": "2025-12-29T10:05:00Z",
    "status": "UP",
    "latency_ms": 120,
    "http_code": 200,
    "error": null
  },
  {
    "checked_at": "2025-12-29T10:04:00Z",
    "status": "DOWN",
    "latency_ms": null,
    "http_code": null,
    "error": "timeout"
  }
]
```

## Errores Comunes (formato)
Formato recomendado:
```json
{
    "error":"error_code",
    "message":"human readable message"
}
```

Códigos comunes:
- **400** invalid_request
- **404** not_found
- **500** internal_error