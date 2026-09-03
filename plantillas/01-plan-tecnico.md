# Plan técnico — [NOMBRE DEL MÓDULO]

| Campo | Valor |
|---|---|
| Autor | |
| Fecha | |
| Estado | Borrador / En revisión / **Aprobado** / Implementado |
| Aprobado por | |

> **Este documento se aprueba ANTES de escribir la primera línea de código.**
> Si el plan no convence en texto, el código no lo va a arreglar.

---

## 1. Problema

Qué duele hoy, en lenguaje de negocio. Sin soluciones todavía.

*Ejemplo: "Matricular a 200 empleados toma dos días porque se hace uno por uno
desde el panel de administración."*

## 2. Objetivo

Qué queremos que sea verdad cuando esto termine. Una o dos frases.

## 3. Alcance

### Qué SÍ entra

- [ ]
- [ ]

### Qué NO entra ⚠️

- [ ]
- [ ]

> **Esta sección es obligatoria y es la que más retrabajo evita.** Lo que no está
> escrito acá como excluido, alguien va a asumir que estaba incluido.

---

## 4. Modelo de datos

### Tablas nuevas

| Tabla | Campos clave | Notas |
|---|---|---|
| | | |

### Cambios a tablas existentes

| Tabla | Cambio | ¿Rompe algo existente? |
|---|---|---|
| | | |

### Migración

- Nombre de la migración:
- ¿Requiere backfill de datos existentes? Sí / No
- **Script de reversa:** *(obligatorio, se escribe ahora, no después)*

---

## 5. API

| Método | Ruta | Qué hace | Permiso requerido |
|---|---|---|---|
| GET | | | |
| POST | | | |

**Contratos de entrada/salida:** describir los DTO, o enlazar al archivo.

---

## 6. Interfaz

- Pantallas nuevas o modificadas:
- Flujo del usuario, paso a paso:
- Acciones que dependen de permisos:

---

## 7. Reglas de negocio

Numeradas, para poder referenciarlas después en la verificación.

1.
2.
3.

**Casos borde:** qué pasa con valores en cero, listas vacías, concurrencia,
duplicados, o cuando el usuario cierra el navegador a mitad del flujo.

---

## 8. Criterios de aceptación

Cada uno debe poder responderse con sí o no. Nada de "funciona bien".

- [ ] Dado [contexto], cuando [acción], entonces [resultado observable]
- [ ] Dado [contexto], cuando [acción], entonces [resultado observable]
- [ ] Un usuario sin el permiso X recibe 403 al intentar [acción]
- [ ] Con [entrada inválida], el sistema responde [error concreto] y no persiste nada

---

## 9. Riesgos y decisiones abiertas

| Riesgo / duda | Impacto | Cómo lo mitigamos | ¿Quién decide? |
|---|---|---|---|
| | | | |

---

## 10. Plan de reversa

Si esto sale mal en producción:

1. **Código:** revertir al despliegue anterior
2. **Base de datos:** ejecutar `[nombre del script de reversa]`
3. **Datos ya creados por el módulo:** qué se hace con ellos
4. **Punto de no retorno:** a partir de qué momento ya no se puede revertir
   limpiamente y por qué

---

## 11. Fases de entrega

Si el módulo es grande, partirlo en entregas que puedan salir a producción por
separado.

| Fase | Qué entrega | ¿Desplegable sola? |
|---|---|---|
| 1 | | |
| 2 | | |

---

## 12. Cierre *(se llena al terminar)*

- **Qué quedó implementado:**
- **Qué quedó fuera y por qué:**
- **Deuda técnica asumida:**
- **Qué aprendimos que cambia el próximo plan:**
