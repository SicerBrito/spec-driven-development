# CONTEXTO DEL PROYECTO — [NOMBRE]

> Este archivo vive en la raíz del repositorio y es lo primero que lee cualquier
> asistente de IA y cualquier desarrollador nuevo. **Mantenlo corto.** El detalle
> pesado va en las guías de referencia, que se cargan solo cuando hacen falta.

---

## ENRUTAMIENTO — lee esto primero

| Cuándo | Archivo a leer |
|--------|----------------|
| Antes de crear un controller, servicio o componente | `docs/referencia/patrones-codigo.md` |
| Antes de tocar permisos o autorización | `docs/referencia/permisos.md` |
| Antes de tocar reportes | `docs/referencia/reportes.md` |
| Antes de tocar [otro tema pesado] | `docs/referencia/[...].md` |

> **Regla:** cuando la tarea entre en uno de esos temas, **abre primero el archivo
> de referencia correspondiente** — no reconstruyas los patrones de memoria.

Esta tabla es la pieza más valiosa del documento: mantiene la raíz liviana y
garantiza que el contexto correcto se cargue en el momento correcto.

---

## QUÉ ES EL PROYECTO

Dos o tres párrafos. Qué resuelve, para quién, y qué lo hace distinto.
Incluye las audiencias del sistema y los productos principales.

---

## STACK

| Capa | Tecnología | Versión |
|---|---|---|
| Backend | | |
| Frontend | | |
| Base de datos | | |
| Autenticación | | |

**Integraciones externas:** servicio, para qué se usa, dónde está configurado.

---

## ESTRUCTURA DE CARPETAS

```
[árbol comentado — solo hasta el nivel que hace falta para orientarse]
```

---

## ARQUITECTURA

Diagrama de una línea del flujo de una petición:

```
Controller → Servicio → Unidad de trabajo → Repositorio → Contexto → BD
```

Y luego lo que **no** es obvio leyendo el código:

- Clases base de las que todo hereda, y qué aportan
- Envoltorio de respuesta estándar de la API
- Comportamiento automático que no se ve (auditoría, filtros globales,
  interceptores)

---

## CONVENCIONES OBLIGATORIAS

Lo que se repite en todo el código y no es negociable. Escribe la regla, no la
explicación.

- Nombres de métodos, rutas y archivos
- Manejo de errores de negocio
- Cómo se exponen los datos (nunca entidades, siempre DTO)
- Cómo se hacen los cambios de esquema

---

## REGLAS DE TRABAJO

Numeradas, en imperativo, sin ambigüedad.

1. **Leer siempre antes de modificar** — nunca editar sin haber leído el archivo.
2. **Seguir los patrones existentes** — antes de crear algo nuevo, revisar uno similar.
3. **Preguntar ante dudas de alcance** — no asumir.
4. **Cambios de esquema = migración versionada, nunca SQL a mano.**
5. [las que apliquen a tu proyecto]

> Las reglas que empiezan con "NUNCA" y "SIEMPRE" son las que más rinden. Si una
> regla se rompió una vez y costó caro, escríbela acá con el motivo.

---

## TRAMPAS CONOCIDAS ⚠️

La sección más subestimada. Cada vez que algo falle de forma no obvia, anótalo:

- **[Trampa]:** qué parece · qué pasa en realidad · qué hacer en su lugar

*Ejemplo: "Los nombres de tabla en SQL crudo NO coinciden con los nombres de
entidad — están renombrados en las configuraciones. Verifica siempre el nombre
real antes de escribir SQL."*
