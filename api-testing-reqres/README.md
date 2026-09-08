# Testing de API — ReqRes (CRUD de usuarios)

**Rol:** QA Tester / QA Automation (proyecto independiente de práctica)
**Fecha:** Septiembre 2026
**API bajo prueba:** [reqres.in](https://reqres.in/) — API REST pública de práctica que simula un CRUD de usuarios
**Tipo de testing:** Testing de API (funcional, códigos de estado, validación de esquema, casos límite)

## Objetivo

Validar el comportamiento de los endpoints principales de un CRUD de usuarios (GET, POST, PUT, DELETE), verificando códigos de estado HTTP, estructura de la respuesta, y manejo de casos límite (recursos inexistentes, payloads incompletos).

## Qué hice

- Diseñé 8 casos de prueba cubriendo las 4 operaciones CRUD más casos límite.
- Ejecuté cada caso con peticiones HTTP reales (`fetch`) y capturé el código de estado y el cuerpo de la respuesta.
- Verifiqué que cada respuesta tuviera los campos esperados en su esquema.
- Identifiqué una observación relevante sobre validación de datos de entrada.

## Casos de prueba y resultados

| ID | Caso de prueba | Endpoint / Método | Resultado esperado | Código obtenido | Estado |
|----|----------------|--------------------|---------------------|-----------------|--------|
| TC-01 | Listar usuarios (paginado) | `GET /api/users?page=2` | 200 OK, arreglo de usuarios no vacío | 200 — 6 usuarios devueltos | ✅ Pass |
| TC-02 | Obtener usuario válido | `GET /api/users/2` | 200 OK, incluye `email` y `avatar` | 200 — ambos campos presentes | ✅ Pass |
| TC-03 | Obtener usuario inexistente | `GET /api/users/999` | 404 Not Found | 404 — cuerpo `{}` | ✅ Pass |
| TC-04 | Crear usuario | `POST /api/users` `{name, job}` | 201 Created, incluye `id` y `createdAt` | 201 — ambos campos presentes | ✅ Pass |
| TC-05 | Actualizar usuario | `PUT /api/users/2` `{name, job}` | 200 OK, incluye `updatedAt` | 200 — campo presente | ✅ Pass |
| TC-06 | Eliminar usuario | `DELETE /api/users/2` | 204 No Content | 204 | ✅ Pass |
| TC-07 | Recurso inexistente | `GET /api/unknown/23` | 404 Not Found | 404 — cuerpo `{}` | ✅ Pass |
| TC-08 | Crear usuario con cuerpo vacío (caso límite) | `POST /api/users` `{}` | 400 Bad Request (debería validar campos requeridos) | 201 Created — sin `name` ni `job` | ⚠️ Observación (ver hallazgo) |

## Evidencia (respuestas reales capturadas)

**TC-04 — POST /api/users**
```json
{
  "name": "Manuel Champutiz",
  "job": "QA Tester",
  "id": "578",
  "createdAt": "2026-09-08T04:04:42.615Z"
}
```

**TC-08 — POST /api/users con cuerpo vacío**
```json
{
  "id": "368",
  "createdAt": "2026-09-08T04:04:43.542Z"
}
```

## Hallazgo

**Falta de validación de campos requeridos en la creación de usuarios.** El endpoint `POST /api/users` acepta un cuerpo vacío `{}` y de todas formas responde `201 Created` con un `id` generado, en lugar de responder `400 Bad Request` exigiendo `name` y `job`. En una API real esto permitiría crear registros incompletos o corruptos en la base de datos.

*Nota: ReqRes es una API de demostración de solo simulación (según su propia documentación, los datos no se persisten realmente), por lo que este hallazgo se documenta como ejercicio de análisis de casos límite y buenas prácticas de validación, no como un bug de producción.*

## Qué aprendí / valor del ejercicio

Los casos "felices" (happy path) de un CRUD suelen funcionar bien; el valor real de un tester está en los casos límite, como probar qué pasa cuando el cliente envía menos información de la esperada. Este ejercicio reforzó mi criterio para distinguir entre "la petición no truena" y "la petición se comporta correctamente".

## Herramientas utilizadas
Peticiones HTTP (`fetch`/JavaScript), inspección de códigos de estado y payloads JSON, diseño de casos de prueba de API, análisis de casos límite. (Los mismos casos pueden replicarse fácilmente en Postman importando cada endpoint.)
