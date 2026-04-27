# Tarea INF331: Diseño, Implementación y Testing de una API de Gestión de Tareas con API Key y documentación API

## 1. Contexto del problema

Una empresa está desarrollando una plataforma interna para gestionar el trabajo de sus equipos. Actualmente, las tareas se administran de forma manual (planillas, correos, chats), lo que genera:

- Falta de trazabilidad  
- Errores en asignaciones  
- Dificultad para conocer estados reales  
- Problemas de coordinación  

El objetivo es construir una API REST de gestión de tareas (Task Management API) que permita administrar tareas, usuarios y estados, incorporando:

- Seguridad básica mediante API Key
- Documentación formal mediante Swagger/OpenAPI
- Estrategia completa de testing de API

---

## 2. Objetivo general

Diseñar, implementar, documentar (Swagger) y probar una API REST para la gestión de tareas, incorporando validaciones, seguridad básica y automatización de pruebas.

---

## 3. Objetivos específicos
Se debe:
- Diseñar una API REST basada en un dominio real (gestión de tareas)
- Implementar endpoints con reglas de negocio claras
- Documentar la API usando Swagger/OpenAPI
- Implementar autenticación mediante API Key
- Diseñar pruebas funcionales, negativas y de seguridad
- Automatizar pruebas de API
- Validar la consistencia entre implementación y documentación

---

# 4. Dominio del problema: Gestión de Tareas

## 4.1 Entidad principal: Task

```json
{
  "id": 1,
  "title": "Implementar endpoint de login",
  "description": "Crear endpoint POST /login con validación",
  "status": "pending",
  "priority": "high",
  "assignedTo": 10,
  "dueDate": "2026-05-10",
  "createdAt": "2026-04-26"
}
```

---

## 4.2 Reglas de negocio

- `title` es obligatorio
- `status` ∈ {pending, in_progress, done}
- `priority` ∈ {low, medium, high}
- `dueDate` no puede ser menor a la fecha actual
- No se puede marcar como `done` si no estuvo antes en `in_progress`
- No se puede eliminar una tarea en estado `in_progress`

---

# 5. Endpoints obligatorios

## 5.1 Health Check

```
GET /health
```

- Público (sin API Key)

---

## 5.2 Listar tareas

```
GET /tasks
```

- Requiere API Key
- Soporta filtros:

```
?status=pending
?priority=high
?assignedTo=10
?limit=10
```

---

## 5.3 Obtener tarea por ID

```
GET /tasks/{id}
```

Casos:

- ID válido → 200  
- No existe → 404  
- Formato inválido → 400  

---

## 5.4 Crear tarea

```
POST /tasks
```

Validaciones:

- Campos obligatorios
- Tipos de datos correctos
- Reglas de negocio

---

## 5.5 Actualizar tarea

```
PATCH /tasks/{id}
```

- Validar transición de estados
- Validar reglas de negocio

---

## 5.6 Eliminar tarea

```
DELETE /tasks/{id}
```

- Restringido según estado

---

## 5.7 Endpoint adicional obligatorio

Ejemplo:

```
POST /tasks/{id}/assign
```

```json
{
  "userId": 10
}
```

---

# 6. Seguridad: API Key

## 6.1 Reglas

Header obligatorio:

```
x-api-key: <API_KEY>
```

Respuestas:

- Sin API Key → `401 Unauthorized`
- API Key inválida → `403 Forbidden`
- API Key válida → acceso permitido

## 6.2 Requisitos técnicos

- API Key en variables de entorno
- Validación centralizada (middleware recomendado)

---

# 7. Documentación obligatoria con Swagger

La API debe estar documentada usando OpenAPI 3.x.

## 7.1 Requisitos mínimos

La documentación Swagger debe incluir:

- Información general de la API
- Definición de endpoints
- Parámetros
- Request bodies
- Response schemas
- Códigos HTTP
- Ejemplos
- Seguridad API Key

---

## 7.2 Seguridad en Swagger

```yaml
components:
  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      in: header
      name: x-api-key
```

---

## 7.3 UI Swagger

Debe exponerse en:

```
/swagger
```

---

# 8. Requerimientos de testing

## 8.1 Tipos de prueba necesarios

- Funcionales
- Negativos
- Borde
- Seguridad
- Contrato
- Automatización

---

## 8.2 Validación Swagger vs API

- Consistencia de schemas
- Códigos HTTP correctos
- Endpoints alineados

---

# 9. Automatización
Los estudiantes deben implementar un conjunto automatizado de pruebas de API que valide el comportamiento funcional, de seguridad y de contrato de la API.

## 9.1 Detalles

#### Qué automatizar
- Health check (`/health`)
- CRUD de tareas
- Reglas de negocio (validaciones y transiciones)
- Seguridad (API Key: 401, 403, acceso válido)
- Contrato (consistencia con Swagger)

#### Requisitos mínimos
- ≥ 10 pruebas automatizadas  
- ≥ 3 endpoints cubiertos  
- Casos positivos y negativos  
- Ejecución con un comando (`pytest`, `npm test`, `newman`, etc.)

#### Buenas prácticas
- Tests independientes  
- Datos controlados  
- Uso de variables (URL, API Key)  
- Organización clara de archivos  

#### Opcional
- CI/CD  
- Reportes  
- Tests paralelos  

#### Objetivo
Asegurar calidad, detectar errores y validar la API de forma sistemática.

## 9.2 Herramientas sugeridas 

- Postman + Newman  
- Pytest + requests  
- Jest + Supertest  
- REST Assured  

#### 9.3 Ejemplo (Python + pytest)

```python
import requests

BASE_URL = "http://localhost:3000"
API_KEY = "test-key"

def test_create_task_success():
    response = requests.post(
        f"{BASE_URL}/tasks",
        headers={"x-api-key": API_KEY},
        json={
            "title": "Test task",
            "status": "pending",
            "priority": "medium"
        }
    )

    assert response.status_code == 201
    assert "id" in response.json()
```
---

# 10. Requerimientos técnicos

- API REST funcional
- JSON
- Validación de inputs
- Manejo correcto de errores

---

## 11. Entregables

Entregar únicamente el **enlace al repositorio en GitHub**, el cual debe contener:

- Código fuente de la API: implementación completa de la API REST, incluyendo endpoints, validaciones, manejo de errores y seguridad con API Key.  
- Swagger/OpenAPI: documentación accesible (por ejemplo en `/swagger` o `/docs`) con endpoints, parámetros, requests, responses, códigos HTTP y seguridad. 
- Plan de pruebas: descripción de la estrategia de testing, alcance, tipos de pruebas, herramientas y criterios de aceptación.  
- Casos de prueba: listado o matriz con identificador, objetivo, pasos, datos de entrada y resultados esperados.  
- Automatización: suite de pruebas automatizadas ejecutable con un comando (ej: `pytest`, `npm test`, `newman`).  
- Evidencia: resultados de ejecución de pruebas (logs, capturas, reportes o salidas de consola).  
- README: instrucciones para instalar, configurar, ejecutar la API, acceder a Swagger y correr las pruebas.

### Resultado esperado

El estudiante integra diseño, implementación, documentación y testing de APIs en un escenario realista.

---
