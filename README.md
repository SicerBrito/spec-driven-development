# Spec-Driven Development asistido por IA

Método y plantillas para construir software con asistencia de IA **sin perder el
criterio técnico**: cada módulo nace de un plan escrito y aprobado antes de la
primera línea de código.

> **In short (EN):** A practical method for AI-assisted development on .NET +
> Angular codebases. Every module starts from a written technical plan with
> acceptance criteria and a rollback plan, backed by a root context document that
> keeps generated code consistent. Templates included.

---

## El problema que resuelve

Cuando le pides código a una IA sin contexto, obtienes código que *funciona* pero
que no se parece al resto del proyecto: otro patrón de repositorio, otra forma de
manejar errores, otro estilo de nombres. Multiplicado por cien tareas, el
proyecto se vuelve un collage.

El error de fondo es tratar a la IA como un autocompletado más rápido. **La IA no
falla por falta de capacidad, falla por falta de contexto y de criterio de
aceptación.**

Este método ataca las dos cosas.

---

## Los tres artefactos

### 1. Documento raíz de contexto

Un solo archivo en la raíz del repositorio que responde: qué es el proyecto, cómo
está organizado, qué convenciones son obligatorias y **a qué guía específica ir**
para cada tipo de tarea.

La parte que más rinde es la tabla de enrutamiento:

| Cuándo | Archivo a leer |
|--------|----------------|
| Antes de crear un controller o servicio | `docs/referencia/patrones-codigo.md` |
| Antes de tocar permisos | `docs/referencia/permisos.md` |
| Antes de tocar reportes | `docs/referencia/reportes.md` |

Así el documento raíz se mantiene corto y las guías pesadas se cargan solo cuando
hacen falta. Sirve igual para una IA que para un desarrollador nuevo.

👉 Plantilla: [`plantillas/00-contexto-proyecto.md`](plantillas/00-contexto-proyecto.md)

### 2. Plan técnico por módulo

Antes de escribir código: qué se va a construir, qué cambia en la base de datos,
qué endpoints aparecen, **qué NO entra en el alcance**, cómo se verifica que
quedó bien y cómo se revierte si sale mal.

Escribirlo toma una o dos horas. Ahorra días.

👉 Plantilla: [`plantillas/01-plan-tecnico.md`](plantillas/01-plan-tecnico.md)

### 3. Checklist de despliegue con reversa

Ningún cambio de esquema sale a producción sin su script de reversa escrito
**antes** de desplegar. Si hay que volver atrás, no se improvisa a las once de la
noche.

👉 Plantilla: [`plantillas/02-checklist-despliegue.md`](plantillas/02-checklist-despliegue.md)

---

## El ciclo

```
1. PLAN        Escribir el plan técnico. Discutirlo. Aprobarlo.
                       ↓
2. CONTEXTO    Cargar el documento raíz + la guía de referencia que aplique.
                       ↓
3. CONSTRUIR   Implementar contra el plan, no contra la intuición.
                       ↓
4. VERIFICAR   Recorrer los criterios de aceptación del plan, uno por uno.
                       ↓
5. DESPLEGAR   Checklist + script de reversa listo antes de tocar producción.
                       ↓
6. AUDITAR     Documento de cierre: qué quedó, qué no, qué deuda se asumió.
```

El paso 1 es el que casi todo el mundo se salta, y es el que hace que los
demás funcionen.

---

## Reglas que hacen la diferencia

**El plan se aprueba antes de escribir código.** Si el plan no convence en texto,
el código no lo va a arreglar.

**El alcance negativo es obligatorio.** La sección "qué NO entra" evita más
retrabajo que cualquier otra parte del documento.

**Un cambio de esquema, una migración versionada, un script de reversa.** Sin
excepciones y sin SQL a mano en producción.

**Las convenciones se escriben, no se explican.** Lo que solo vive en la cabeza
del líder se pierde en la primera tarea delegada — a una persona o a una IA.

**La IA propone, tú apruebas.** El criterio técnico no se delega. El método
existe justamente para que puedas ejercerlo con la información completa.

---

## Resultado medible

Aplicando esto sobre una plataforma SaaS multi-tenant en producción:

- **20 módulos de negocio** sostenidos por un equipo de **2 personas**
- **~197.000 líneas** de código propio (C#, TypeScript, HTML, SCSS)
- **146 migraciones** versionadas, todas con reversa
- **6 planes técnicos** (~190 KB de especificación) escritos antes de implementar
- **3 entornos** en operación continua desde 2024

---

## Contenido del repositorio

```
plantillas/
├── 00-contexto-proyecto.md      Documento raíz de arquitectura y convenciones
├── 01-plan-tecnico.md           Plan por módulo, con alcance negativo y reversa
└── 02-checklist-despliegue.md   Pasos de despliegue y plan de reversa
ejemplo/
└── plan-tecnico-ejemplo.md      Plan completo de un módulo de ejemplo
```

👉 **[Lee el ejemplo completo](ejemplo/plan-tecnico-ejemplo.md)** antes que las
plantillas. Muestra cómo el caso de concurrencia que habría roto el cobro
apareció **al escribir los criterios de aceptación**, no al programar.

Las plantillas están en español y con ejemplos de .NET + Angular, pero el método
no depende del stack.

---

## Licencia

MIT — úsalo, adáptalo, cámbiale lo que no te sirva.

Escrito por [Sicer Andrés Brito Gutiérrez](https://github.com/SicerBrito) a
partir de la práctica real de construir y operar un producto en producción.
