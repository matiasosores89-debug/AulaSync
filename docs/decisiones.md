# Decisiones de producto y modelo

Validación de la versión 1 contra la Descripción del Sistema, Análisis del Modelo, Historias de Usuario, DER, DFD y Product Backlog. Las copias de referencia se encuentran en [`docs/requisitos`](requisitos/).

## Alcance aprobado

- Actor humano: estudiante. AulaSync mantiene un espacio personal por cuenta.
- Funciones: registro y acceso; gestión de materias; registro y consulta de actividades por materia; carga y consulta de PDF/texto; generación, guardado y consulta de resúmenes con un servicio de IA externo.
- Fuera de alcance: docentes y administración, notificaciones, calendario, recuperación de contraseña, colaboración, OCR y otras funciones no descritas.
- Se retiró **Simulacro de examen**, que estaba expuesto en la navegación y no pertenece a las historias ni al Product Backlog suministrado.

## Relaciones y cardinalidades

| Relación          | Decisión para V1                                                     | Correspondencia con la referencia                                                                 |
| ----------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Usuario–Materia   | 1:N; materia con propietario obligatorio                             | Coincide con Análisis y DER                                                                       |
| Materia–Actividad | 1:N; actividad siempre asociada a una materia                        | Coincide con HU-04 y DER                                                                          |
| Usuario–Material  | 1:N; propietario obligatorio                                         | Coincide con Análisis y DER                                                                       |
| Materia–Material  | Materia opcional, pero si se indica debe pertenecer al mismo usuario | HU-05 dice «cuando corresponda»; el DER propone opcionalidad                                      |
| Material–Resumen  | 0..1 resumen guardado por material                                   | Se acepta para V1 la cardinalidad propuesta en Análisis/DER y la unicidad existente en PostgreSQL |

La autenticación obtiene la identidad de la sesión, nunca del cuerpo de una petición. Una FK compuesta asegura también a nivel de base que material y materia compartan propietario. Borrar una materia elimina sus actividades y la transacción del servicio desvincula sus materiales para conservar el material y su resumen. Borrar un material elimina su resumen.

## Decisiones de atributos

- Los tipos de actividad son tarea, trabajo práctico y examen; la fecha se persiste como `DATE`.
- El DER propone un campo `estado`, pero no especifica valores ni transiciones y HU-04 no lo pide. Se omite en V1 para no inventar comportamiento. Si la evaluación académica exige estado, definir valores y reglas antes de una migración.
- Un material acepta texto o PDF; asociarlo a materia es opcional. Los PDF se guardan como `BYTEA`, hasta 5 MiB. Se extraen hasta 8.000 caracteres y 60 páginas. Los PDF escaneados no son procesables sin OCR.
- Un resumen solo se guarda tras una generación correcta y queda asociado a su material. La lectura posterior consulta PostgreSQL sin llamar otra vez al proveedor.
- `Sesion` es almacenamiento técnico de autenticación, no una entidad académica ni un nuevo actor.

## Trazabilidad funcional

| Historia      | Implementación                                                  |
| ------------- | --------------------------------------------------------------- |
| HU-01 y HU-02 | Registro, login/logout, sesión persistida y aislamiento         |
| HU-03         | Alta, consulta, edición y eliminación de materias propias       |
| HU-04         | Alta y consulta de tarea, trabajo práctico y examen por materia |
| HU-05         | Carga y consulta de PDF y texto; materia opcional               |
| HU-06         | Generación de resumen y manejo de errores del proveedor         |
| HU-07         | Guardado de borrador firmado, con unicidad por material         |
| HU-08         | Consulta de resumen por propietario sin reprocesamiento         |

El DFD se mantiene como modelo de flujos: el estudiante interactúa con procesos de AulaSync, y el proceso de resúmenes intercambia contenido/resultado con el servicio externo. No se interpreta como navegación de pantallas.

## Incidencia de calidad conocida

En la prueba documentada de _Introducción al Derecho_, la extracción incluyó contenido editorial e índice y el resumen cubrió poco el cuerpo del libro. El proveedor local actual reconstruye el resultado con fragmentos del original para evitar afirmaciones ajenas, pero eso no garantiza cobertura representativa. La corrección necesita una evaluación con el documento original y criterios de calidad; el OCR sigue fuera de alcance.
