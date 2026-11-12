# Plan y estado de implementación

La trazabilidad se cotejó con el Product Backlog original, disponible en [`docs/requisitos/AulaSync_Product_Backlog.pdf`](requisitos/AulaSync_Product_Backlog.pdf). El estado se refiere al código y a la evidencia registrada; las verificaciones pendientes del entorno se señalan por separado en [validación](validacion.md).

| Sprint | Alcance del backlog                               | Estado                                                                                                                                                                                                                         |
| ------ | ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1      | Base del proyecto, estructura, datos y criterios  | Implementado; PostgreSQL y relaciones verificados históricamente. La revisión visual original se completó parcialmente en revisiones posteriores.                                                                              |
| 2      | HU-01/02/03: cuentas, sesiones y materias         | Implementado y probado con PostgreSQL, aislamiento y smoke HTTP.                                                                                                                                                               |
| 3      | HU-04: actividades                                | Implementado y probado; consulta y alta por materia propia.                                                                                                                                                                    |
| 4      | HU-05: materiales                                 | Implementado; texto y PDF privados, carga y consulta. Prueba manual del usuario registrada.                                                                                                                                    |
| 5      | HU-06/07/08: resúmenes                            | Implementado y probado con proveedor simulado y Ollama extractivo. Incidencia de cobertura semántica conocida.                                                                                                                 |
| 6      | Integración, regresión, aislamiento y errores     | Implementado; la evidencia histórica registra 69 pruebas y 14 comprobaciones relacionales en entorno aislado. La revisión actual pasó 29 pruebas de frontend y 2 unitarias de backend; no pudo repetir las pruebas integrales. |
| 7      | Aceptación, documentación, demostración y paquete | Cotejo funcional/documental completado; revisión visual final, Ollama real y auditoría actual de npm siguen pendientes por configuración local ausente.                                                                        |

## Sprint 7: cierre contra T7.1–T7.5

| Tarea                        | Resultado                                                                                                                                                                                                                                        |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| T7.1 Validar alcance         | HU-01 a HU-08 cotejadas con el código, el DER y el DFD. Se retiró el simulacro, que no pertenecía al alcance aprobado. Se decidió omitir `Actividad.estado` en V1 porque la referencia no define estados ni transiciones y HU-04 no lo requiere. |
| T7.2 Completar documentación | README, decisiones, plan, API y validación actualizados para reflejar el sistema implementado y las limitaciones. Los seis PDF se incluyen en `docs/requisitos/`.                                                                                |
| T7.3 Preparar demostración   | La guía manual de Sprint 2 sigue cubriendo el recorrido de acceso y materias. El README resume los flujos posteriores; falta la revisión visual final del recorrido integral.                                                                    |
| T7.4 Registrar pendientes    | Se conservan la incidencia de calidad de resumen, el chequeo actual de dependencias, la prueba local de Ollama y la revisión visual.                                                                                                             |
| T7.5 Preparar paquete        | Código y documentación quedan autocontenidos en el proyecto. No se declara despliegue de producción ni aceptación visual final.                                                                                                                  |

## Decisiones funcionales cotejadas

- La identidad y propiedad de los datos se derivan de la sesión. El servicio de IA es externo y recibe solo el contenido seleccionado.
- Las actividades requieren materia; los materiales pueden no tenerla. El borrado de materia elimina actividades y conserva materiales/resúmenes desvinculándolos.
- Se mantiene como decisión de V1 un único resumen guardado por material, garantizado por la restricción única existente.
- No se añade estado a las actividades hasta definir valores y comportamiento; tampoco se incorporan OCR, notificaciones, calendario ni roles administrativos.

## Evidencia previa de Sprint 6

`docs/validacion.md` conserva la última revalidación integral anterior: 39/39 pruebas de backend, 30/30 de frontend y 14 comprobaciones relacionales. En esta revisión pasaron typecheck, build, formato, validación del esquema, 29 pruebas de frontend y 2 unitarias de backend. No hay `TEST_DATABASE_URL` ni clúster aislado en `127.0.0.1:5437`, por lo que la integración de backend y `db:check` siguen pendientes de repetición.
