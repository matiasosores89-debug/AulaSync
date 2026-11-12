# Guía de demostración de AulaSync

## Preparación

1. En una terminal, inicia PostgreSQL local y AulaSync según el [README](../README.md). No ejecutes `seed`, `reset` ni `db push` sobre una base con datos de origen desconocido.
2. Abre `http://127.0.0.1:5173` y crea una cuenta de demostración. El registro inicia la sesión.
3. Para demostrar el flujo sin un modelo local, configura `AI_PROVIDER=mock` solo en un entorno de desarrollo; la interfaz identifica claramente la respuesta como simulada. Para probar IA real, inicia Ollama en `127.0.0.1:11435` con el modelo configurado en `backend/.env`.

## Recorrido principal

1. En **Materias**, crea una materia, edita su descripción y vuelve al inicio para comprobar que aparece en el espacio personal.
2. En **Actividades**, selecciona esa materia y registra una tarea, trabajo práctico o examen con fecha. Comprueba el listado por materia.
3. En **Materiales**, carga un texto de estudio o un PDF con texto seleccionable; asócialo a la materia si corresponde. Comprueba que se puede volver a consultar.
4. En la tarjeta del material, genera un resumen. Revisa el borrador y guárdalo explícitamente.
5. En **Resúmenes**, abre el resumen y comprueba su material de origen. La consulta debe recuperar lo guardado sin otra generación.
6. Cierra sesión y vuelve a ingresar. Comprueba que tus datos persisten.
7. Para demostrar aislamiento, ingresa con otra cuenta en una ventana privada. No debe poder consultar ni descargar los recursos de la primera cuenta.

## Qué observar

- Los recursos se filtran por la sesión autenticada; una materia asociada debe ser propia.
- Los errores de acceso, carga, procesamiento y validación se presentan sin exponer detalles internos.
- Los borradores no se guardan hasta que el estudiante elige guardar.
- Los PDF escaneados sin capa de texto no generan resumen porque V1 no incluye OCR.
- El proveedor `mock` solo demuestra el flujo; no representa una síntesis generada por IA. Los resúmenes requieren revisión del estudiante, especialmente en documentos con portadas, datos editoriales e índices.
