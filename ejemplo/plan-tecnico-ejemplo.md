# Plan técnico — Cupones de descuento

| Campo | Valor |
|---|---|
| Autor | Sicer Brito |
| Fecha | 2026-03-10 |
| Estado | **Aprobado** |
| Aprobado por | Product Owner |

> **Ejemplo ficticio.** Sirve para mostrar el nivel de detalle que hace útil a la
> plantilla. Los datos y el producto son inventados.

---

## 1. Problema

Comercial quiere hacer campañas de descuento, pero hoy la única forma de rebajar
un precio es que un administrador edite el valor del curso a mano. Eso cambia el
precio para **todos**, no queda rastro de quién autorizó la rebaja, y hay que
acordarse de devolverlo al valor original cuando termina la campaña. Ya pasó dos
veces que se olvidó.

## 2. Objetivo

Que Comercial pueda crear códigos de descuento con vigencia y tope de usos, que
el estudiante aplique al pagar, sin tocar el precio del curso y dejando registro
de cada uso.

## 3. Alcance

### Qué SÍ entra

- [x] CRUD de cupones para el rol Comercial
- [x] Descuento fijo (monto) o porcentual
- [x] Fecha de inicio, fecha de fin y tope de usos
- [x] Aplicación del cupón en el flujo de pago
- [x] Registro de cada uso: quién, cuándo, sobre qué orden
- [x] Reporte de uso por cupón

### Qué NO entra ⚠️

- [ ] Cupones de un solo uso por usuario *(requiere reglas por persona — fase 2)*
- [ ] Cupones aplicables solo a ciertos cursos *(por ahora aplican a todo)*
- [ ] Cupones acumulables *(uno por orden, y punto)*
- [ ] Generación masiva de códigos únicos
- [ ] Notificación por correo al crear un cupón

> Sin esta sección, "cupones" significa cinco cosas distintas para cinco personas.

---

## 4. Modelo de datos

### Tablas nuevas

| Tabla | Campos clave | Notas |
|---|---|---|
| `Cupones` | `IdCupon`, `Codigo`, `Tipo`, `Valor`, `FechaInicio`, `FechaFin`, `TopeUsos`, `UsosActuales` | `Codigo` con índice único |
| `UsosCupon` | `IdUsoCupon`, `IdCupon`, `IdUsuario`, `IdOrden`, `MontoDescontado`, `FechaUso` | Un registro por aplicación efectiva |

### Cambios a tablas existentes

| Tabla | Cambio | ¿Rompe algo existente? |
|---|---|---|
| `Ordenes` | `IdCupon` nullable, `MontoDescuento` con valor por defecto 0 | No — nullable y con default |

### Migración

- Nombre: `AgregarCuponesDescuento`
- Backfill: no necesario (`MontoDescuento` arranca en 0)
- **Script de reversa:** `docs/reversa/cupones-reversa.sql` — elimina las dos
  tablas nuevas y las dos columnas de `Ordenes`

---

## 5. API

| Método | Ruta | Qué hace | Permiso |
|---|---|---|---|
| GET | `/api/cupon` | Lista paginada con filtros | `comercial.cupones.ver` |
| POST | `/api/cupon` | Crea un cupón | `comercial.cupones.crear` |
| PUT | `/api/cupon/{id}` | Edita un cupón sin usos | `comercial.cupones.editar` |
| DELETE | `/api/cupon/{id}` | Desactiva (no borra) | `comercial.cupones.eliminar` |
| POST | `/api/cupon/validar` | Valida un código y devuelve el descuento | *(autenticado)* |
| GET | `/api/cupon/{id}/usos` | Reporte de uso | `comercial.cupones.ver` |

`POST /api/cupon/validar` recibe `{ codigo, montoOrden }` y devuelve
`{ valido, motivo, montoDescuento, montoFinal }`.

---

## 6. Interfaz

- **Comercial › Cupones** — lista con filtros por estado y vigencia
- **Formulario de cupón** — tipo, valor, vigencia, tope de usos
- **Detalle de uso** — tabla de quién usó el cupón y cuándo
- **Pago** — campo "¿Tienes un cupón?" con botón Aplicar, que muestra el
  descuento calculado antes de confirmar

---

## 7. Reglas de negocio

1. El código es único, sin distinguir mayúsculas, y se guarda en mayúsculas.
2. Un cupón porcentual no puede pasar de 100.
3. Un cupón fijo nunca deja la orden por debajo de cero: el descuento se topa al
   monto de la orden.
4. Un cupón fuera de vigencia se rechaza con motivo `VENCIDO`.
5. Un cupón con `UsosActuales >= TopeUsos` se rechaza con motivo `AGOTADO`.
6. **Un cupón editado pierde validez sobre órdenes ya pagadas:** los usos
   registrados no se recalculan nunca.
7. Un cupón con al menos un uso **no se puede editar**, solo desactivar.
8. Una orden admite un solo cupón.

**Casos borde:**

- **Concurrencia sobre el último uso disponible.** Dos personas aplican el cupón
  con `TopeUsos - UsosActuales = 1`. El incremento de `UsosActuales` se hace con
  actualización condicional (`WHERE UsosActuales < TopeUsos`); si afecta cero
  filas, se rechaza con `AGOTADO`.
- **Validación y pago separados en el tiempo.** El usuario valida el cupón, se
  demora veinte minutos y paga cuando ya venció. El cupón **se vuelve a validar
  al confirmar el pago**, no solo al aplicarlo en pantalla.
- **Descuento del 100 %.** La orden queda en cero y no debe ir a la pasarela: se
  marca como pagada directamente.

---

## 8. Criterios de aceptación

- [ ] Dado un cupón porcentual del 20 % y una orden de $100.000, cuando se
      aplica, entonces el monto final es $80.000
- [ ] Dado un cupón fijo de $50.000 y una orden de $30.000, cuando se aplica,
      entonces el monto final es $0 y nunca negativo
- [ ] Dado un cupón vencido ayer, cuando se aplica, entonces se rechaza con
      motivo `VENCIDO`
- [ ] Dado un cupón con tope 10 y 10 usos, cuando se aplica, entonces se rechaza
      con motivo `AGOTADO`
- [ ] Dadas dos peticiones simultáneas sobre el último uso disponible, entonces
      solo una tiene éxito y `UsosActuales` queda exactamente en el tope
- [ ] Dado un cupón con usos registrados, cuando se intenta editar, entonces
      responde 409 con un mensaje que explica que solo puede desactivarse
- [ ] Un usuario sin `comercial.cupones.crear` recibe 403 al crear un cupón
- [ ] Un cupón del 100 % marca la orden como pagada sin pasar por la pasarela

---

## 9. Riesgos y decisiones abiertas

| Riesgo | Impacto | Mitigación | ¿Quién decide? |
|---|---|---|---|
| Código de cupón filtrado públicamente | Pérdida de ingresos | Tope de usos obligatorio, sin excepción | Comercial |
| Comercial espera cupones por curso | Retrabajo | Declarado fuera de alcance y confirmado por escrito | ✅ Resuelto |
| Doble descuento por reintento de pago | Cobro incorrecto | El uso se registra al confirmar el pago, no al aplicar | Técnico |

---

## 10. Plan de reversa

1. **Código:** revertir al despliegue anterior
2. **Base de datos:** ejecutar `docs/reversa/cupones-reversa.sql`
3. **Datos creados:** las órdenes con descuento ya aplicado quedan con su monto
   final; se pierde la trazabilidad de qué cupón lo generó
4. **Punto de no retorno:** desde la primera orden real pagada con descuento.
   Después de eso, revertir implica perder información contable — a partir de ahí
   se corrige hacia adelante, no hacia atrás

---

## 11. Fases de entrega

| Fase | Qué entrega | ¿Desplegable sola? |
|---|---|---|
| 1 | Modelo, CRUD y permisos, sin aplicación en pago | Sí |
| 2 | Validación y aplicación en el flujo de pago | Sí |
| 3 | Reporte de uso | Sí |

---

## 12. Cierre

- **Qué quedó implementado:** las tres fases, según lo planeado.
- **Qué quedó fuera y por qué:** cupones por curso y de un solo uso por usuario,
  declarados fuera de alcance desde el inicio. Comercial los pidió otra vez en la
  semana 2; se sostuvo el alcance y quedaron para un plan aparte.
- **Deuda técnica asumida:** el reporte de uso lee directo de `UsosCupon` sin
  caché. Con volúmenes altos habrá que precalcularlo.
- **Qué aprendimos:** el caso de concurrencia sobre el último uso no estaba en el
  primer borrador del plan; salió al escribir los criterios de aceptación. **Ese
  es el argumento de todo el método**: la hora que costó escribir el plan evitó
  un error de cobro en producción.
