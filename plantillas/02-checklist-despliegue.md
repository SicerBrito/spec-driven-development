# Checklist de despliegue — [MÓDULO] · [FECHA]

| Campo | Valor |
|---|---|
| Entorno | Pruebas / **Producción** |
| Plan técnico asociado | |
| Responsable | |
| Ventana estimada | |

---

## Antes de tocar nada

- [ ] El plan técnico está en estado **Aprobado**
- [ ] Todos los criterios de aceptación se verificaron en el entorno de pruebas
- [ ] **El script de reversa está escrito y probado** — no basta con que exista
- [ ] Copia de seguridad de la base de datos, con hora anotada: `________`
- [ ] Se avisó a quien deba saber que habrá una ventana de despliegue

---

## Orden de ejecución

El orden importa: si el código nuevo sale antes que el esquema, rompe.

1. [ ] Detener el servicio de aplicación
2. [ ] Ejecutar la migración de base de datos
3. [ ] Verificar el esquema (consulta de comprobación: `________`)
4. [ ] Desplegar backend
5. [ ] Desplegar frontend
6. [ ] Levantar el servicio
7. [ ] Ejecutar datos semilla o de configuración, si aplica

---

## Verificación en caliente

- [ ] La aplicación responde y el login funciona
- [ ] El flujo principal del módulo nuevo funciona de punta a punta
- [ ] **Un flujo antiguo que no debía cambiar sigue funcionando** *(esto es lo
      que más se olvida y donde más se rompe)*
- [ ] Los registros de log no muestran errores nuevos
- [ ] Los permisos aplican: un usuario sin el permiso no ve ni puede ejecutar la
      acción

---

## Si algo sale mal

1. Detener el servicio
2. Ejecutar el script de reversa: `________`
3. Volver al despliegue anterior
4. Levantar y verificar que el flujo antiguo responde
5. Anotar qué falló, antes de que se olvide

**Punto de no retorno:** ______________________
*(a partir de aquí revertir ya no es limpio — normalmente cuando usuarios reales
han generado datos con el módulo nuevo)*

---

## Cierre

- [ ] Módulo funcionando en producción
- [ ] Documentación actualizada
- [ ] Plan técnico marcado como Implementado y sección de cierre llena

**Notas de lo que pasó:**
