# Proyecto Power BI - mi-proyecto-powerbi

Repositorio inicial con las medidas DAX y la estructura recomendada para un proyecto Power BI.

## Contenido
- `measures.dax`  — archivo con todas las medidas DAX (listas para copiar/pegar en Power BI / Tabular Editor).
- `measures.md`   — documentación en Markdown donde cada medida aparece con su DAX y una breve descripción.
- `queries/`      — carpeta para consultas Power Query (M).
- `model/`        — carpeta para plantillas `.pbit` (no incluye datos).
- `data/sample/`  — datos de ejemplo anónimos (si los agregás, no subir datos sensibles).
- `exports/`      — capturas o PDFs de los reportes.
- `.gitignore`    — reglas básicas para no subir archivos innecesarios.

## Cómo usar (flujo rápido para principiantes)
1. Descargá y descomprimí el archivo entregado (o cloná el repo si ya lo subiste a GitHub).
2. Abrí Power BI Desktop y, si querés, pegá las medidas desde `measures.dax` o usá `measures.md` para leer la documentación de cada medida.
3. Para compartir la estructura sin datos, generá un `.pbit` (Archivo → Exportar → Power BI template) y guardalo en `model/`.
4. Si querés subir todo a GitHub y sos principiante: usá la Web UI de GitHub (crear repo → Add file → Upload files) para archivos pequeños, o GitHub Desktop para subir carpetas completas.
   - Si querés subir `.pbix` grandes ( >100 MiB ), usá Git LFS o alojalo en Drive/OneDrive y poné el link en el README.

## Buenas prácticas rápidas
- No subir datos con PII (nombres reales, CUIT/CUIL, mails, etc.).
- Versioná las medidas y queries como texto (`measures.dax`, `queries/*.m`) para poder ver diffs.
- Commits pequeños y con mensajes claros.
- Mantener `model/` con `.pbit` en lugar de `.pbix` cuando compartas la estructura.

---
Si querés, puedo: 
- Generar un `README.md` con tu descripción personalizada (pegame el texto que quieras). 
- Preparar `measures.md` más detallado (con ejemplos de uso o tests).
- Generar un `.pbit` de ejemplo (necesitaría el `.pbix` o confirmación para crear un template).
