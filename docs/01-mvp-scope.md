# Alcance del MVP (Minimum Viable Product)

## Objetivo del MVP

El objetivo del MVP de UptimeGuard es construir un sistema **mínimo pero funcional** que permita:

- Definir servicios a monitorizar (URLs HTTP)
- Ejecutar comprobaciones periódicas de disponibilidad
- Registrar resultados de cada comprobación
- Enviar alertas cuando el estado del servicio cambia

El MVP debe ser **usable, desplegable y comprensible**, tanto a nivel funcional como educativo.

---

## Funcionalidades incluidas en el MVP

### 1. Gestión de checks (API)
El sistema permite:

- Crear un check con:
  - nombre
  - URL
  - intervalo de comprobación
  - timeout
- Listar checks existentes
- Activar o desactivar un check
- Eliminar un check

---

### 2. Ejecución periódica de checks
Un proceso independiente (worker):

- Ejecuta comprobaciones HTTP según el intervalo definido
- Mide latencia de respuesta
- Detecta si el servicio está disponible o no
- Registra cada ejecución como un resultado

---

### 3. Persistencia de datos
El sistema almacena:

- Definición de checks
- Estado actual de cada check (UP / DOWN / UNKNOWN)
- Historial básico de resultados

La base de datos utilizada en el MVP es simple y local, pensada para facilitar el aprendizaje.

---

### 4. Alertas por cambio de estado
El sistema envía alertas cuando:

- Un servicio pasa de disponible a no disponible
- Un servicio se recupera tras una caída

Las alertas se envían mediante un webhook externo.

No se envían alertas repetidas si el estado no cambia.

---

### 5. Operabilidad mínima
El MVP incluye:

- Endpoint de healthcheck
- Logs visibles desde el entorno de ejecución
- Configuración mediante variables de entorno
- Arranque completo con un solo comando

---

## Funcionalidades excluidas del MVP

Las siguientes funcionalidades **no forman parte del MVP**, aunque podrían añadirse en el futuro:

- Interfaz web (dashboard)
- Autenticación y gestión de usuarios
- Multi-tenant o multi-organización
- Escalado automático
- Orquestación con Kubernetes
- Integraciones avanzadas (email, SMS, Slack, etc.)
- Checks avanzados (TLS, DNS, keyword matching)
- Configuración compleja de alertas
- Alta disponibilidad

Estas exclusiones son **decisiones conscientes** para mantener el proyecto pequeño, enfocado y didáctico.

---

## Criterios de éxito del MVP

El MVP se considera completo cuando:

- El sistema puede ejecutarse localmente sin configuración compleja
- Es posible crear y ejecutar checks reales
- Se reciben alertas ante caídas y recuperaciones
- La arquitectura es clara y comprensible
- El proyecto puede utilizarse como base de aprendizaje DevOps

---

## Principios del MVP

- Simplicidad antes que optimización
- Claridad antes que complejidad
- Funcionalidad real antes que características adicionales
- Documentación como parte del producto
