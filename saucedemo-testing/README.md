# Testing Funcional y Exploratorio — Swag Labs (SauceDemo)

**Rol:** QA Tester (proyecto independiente de práctica)
**Fecha:** Septiembre 2026
**Sitio bajo prueba:** [saucedemo.com](https://www.saucedemo.com/) — e-commerce de práctica ampliamente usado en la industria de QA
**Tipo de testing:** Manual, funcional y exploratorio (login, catálogo, carrito, checkout)

## Objetivo

Validar los flujos críticos de un e-commerce (autenticación, listado de productos, carrito y checkout) usando los distintos perfiles de usuario que ofrece el sitio, cada uno diseñado para exponer un tipo distinto de comportamiento (usuario estándar, usuario bloqueado, usuario con errores de UI, usuario con errores funcionales).

## Qué hice

- Diseñé 10 casos de prueba cubriendo login (positivo y negativo), listado/ordenamiento de productos, carrito y checkout.
- Ejecuté los casos manualmente contra 4 perfiles de usuario distintos (`standard_user`, `locked_out_user`, `problem_user`, `error_user`).
- Encontré y documenté **3 bugs reproducibles** con pasos de reproducción, resultado esperado vs. real, y severidad.
- Verifiqué cálculos de negocio (impuesto y total en checkout).

## Casos de prueba

| ID | Caso de prueba | Usuario | Pasos | Resultado esperado | Resultado real | Estado |
|----|----------------|---------|-------|---------------------|-----------------|--------|
| TC-01 | Login con usuario bloqueado | locked_out_user | Ingresar usuario/contraseña válidos → Login | Mensaje de error claro, sin acceso | "Epic sadface: Sorry, this user has been locked out." — correcto | ✅ Pass |
| TC-02 | Login con usuario estándar | standard_user | Ingresar credenciales válidas → Login | Redirige a página de Productos | Redirige correctamente | ✅ Pass |
| TC-03 | Visualización de imágenes de producto | problem_user | Login → observar imágenes en listado de Productos | Cada producto muestra su imagen real | Todas las imágenes muestran la misma foto de un perro | ❌ Fail (ver BUG-01) |
| TC-04 | Ordenar productos por precio (menor a mayor) | problem_user | Login → abrir filtro → seleccionar "Price (low to high)" | La lista se reordena de menor a mayor precio | El orden no cambia; permanece igual que por defecto | ❌ Fail (ver BUG-02) |
| TC-05 | Agregar producto al carrito | standard_user | Login → "Add to cart" en un producto | El contador del carrito incrementa a 1 | Funciona correctamente | ✅ Pass |
| TC-06 | Completar checkout (información) | error_user | Login → agregar producto → Checkout → llenar First/Last Name y Zip → Continue | Avanza a "Checkout: Overview" | Avanza correctamente | ✅ Pass |
| TC-07 | Cálculo de impuestos y total | error_user | En "Checkout: Overview" revisar Item total, Tax y Total | Tax = 8% del subtotal; Total = subtotal + tax | $29.99 + $2.40 tax = $32.39 — correcto | ✅ Pass |
| TC-08 | Finalizar compra (Finish) | error_user | En "Checkout: Overview" → clic en "Finish" | Navega a página de confirmación "Thank you for your order" | La página no cambia; no hay confirmación ni vaciado del carrito | ❌ Fail (ver BUG-03) |
| TC-09 | Remover producto del carrito | standard_user | Agregar producto → ir a carrito → "Remove" | El producto desaparece del carrito | Funciona correctamente | ✅ Pass |
| TC-10 | Login con credenciales inválidas | N/A | Ingresar usuario/contraseña inexistentes → Login | Mensaje de error, sin acceso | Mensaje de error mostrado correctamente | ✅ Pass |

## Bugs encontrados

### BUG-01 — Imágenes de producto incorrectas para `problem_user`
- **Severidad:** Media (defecto visual/de datos, no bloquea la compra pero afecta la confianza del usuario)
- **Pasos para reproducir:** Iniciar sesión con `problem_user` / `secret_sauce` → ir a la página de Productos.
- **Resultado esperado:** Cada producto muestra su imagen correspondiente (mochila, luz de bicicleta, camiseta, etc.).
- **Resultado real:** Todos los productos muestran la misma imagen de un perro con una pelota de tenis.
- **Evidencia:** verificado visualmente comparando el listado completo de 6 productos.

### BUG-02 — El ordenamiento por precio no funciona para `problem_user`
- **Severidad:** Alta (funcionalidad rota: el usuario no puede confiar en el filtro de orden)
- **Pasos para reproducir:** Iniciar sesión con `problem_user` → abrir el filtro (ícono de embudo) → seleccionar "Price (low to high)".
- **Resultado esperado:** Los productos se reordenan de $7.99 a $49.99.
- **Resultado real:** El orden permanece idéntico al orden por defecto (Backpack $29.99, Bike Light $9.99, Bolt T-Shirt $15.99, Fleece Jacket $49.99, Onesie $7.99, Test.allTheThings $15.99) — no hay reordenamiento visible.
- **Evidencia:** texto de la página extraído antes y después de aplicar el filtro; el orden no cambió.

### BUG-03 — El botón "Finish" no completa la compra para `error_user`
- **Severidad:** Crítica (bloquea completamente el flujo de compra — el usuario no puede finalizar su pedido)
- **Pasos para reproducir:** Iniciar sesión con `error_user` → agregar un producto al carrito → Checkout → completar datos → Continue → en "Checkout: Overview" hacer clic en "Finish".
- **Resultado esperado:** La aplicación navega a la página de confirmación "Thank you for your order" y vacía el carrito.
- **Resultado real:** La página permanece en "Checkout: Overview" sin ningún cambio; el carrito sigue mostrando 1 producto.
- **Evidencia:** contenido de la página verificado antes y después del clic — sin cambios.

## Qué aprendí / valor del ejercicio

Practicar con perfiles de usuario diseñados para fallar de formas distintas me obligó a comparar sistemáticamente el comportamiento esperado contra el real en vez de asumir que "si no se cae, funciona" — el bug más grave (BUG-03) es silencioso: no hay ningún mensaje de error, solo una acción que no tiene efecto, que es exactamente el tipo de defecto que un test superficial pasaría por alto.

## Herramientas utilizadas
Testing manual, diseño de casos de prueba, testing exploratorio, análisis de severidad, documentación de bugs, navegador (automatizado con asistencia de IA para la ejecución).
