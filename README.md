# 📊 Proyecto BI – Ventas & Clientes

## 📌 Descripción General del Proyecto
Este proyecto presenta un **Dashboard Avanzado de Ventas en Power BI**, que incorpora **bookmarks dinámicos**, **cálculos complejos en DAX** y técnicas de **storytelling interactivo** para analizar el rendimiento comercial.  
Está diseñado para dar seguimiento a métricas financieras clave y ofrecer insights tanto a nivel **ejecutivo** como **operacional**.

---

## 📊 Vista Previa del Dashboard
👉 (Aquí se puede incluir un link a la versión publicada en Power BI Service o capturas en `/docs/Screenshots`).

---

## 🏢 Caso de Uso en la Industria
El análisis del desempeño en ventas es crítico para organizaciones que buscan **optimizar ingresos**, **monitorear tendencias** y **mejorar la toma de decisiones estratégicas**.  
Este proyecto aplica **mejores prácticas de Business Intelligence (BI)** y **visualización de datos** para entregar insights accionables sobre crecimiento interanual, satisfacción de clientes y rentabilidad.

---

## 🔹 Funcionalidades y Características Principales

### 📌 Insights Ejecutivos
- **Análisis de Crecimiento YoY**: tendencias de crecimiento usando `REMOVEFILTERS()` para asegurar visibilidad global.  
- **KPIs de Ventas Netas, Brutas y Egresos**: con comparaciones históricas.  
- **Participación de Productos**: proporción de ventas por categoría o producto.  

### 📌 Insights Operativos
- **Análisis Mensual y Anual**: evolución de ventas a lo largo del tiempo, sensible a filtros y segmentaciones.  
- **Navegación Dinámica con Bookmarks**: storytelling interactivo para usuarios no técnicos.  
- **Ranking de Clientes y Productos**: posiciones dinámicas con variación respecto al año anterior.  
- **Validación de Datos**: medidas para detectar devoluciones, registros con importe negativo o sin cliente.  

### 📌 Cálculos Avanzados en DAX
- **Crecimiento YoY Dinámico**:  
  - Vista ejecutiva → ignora filtros de usuario.  
  - Vista operativa → respeta segmentaciones.  
- **Ventas Acumuladas Proyecto**: cálculo acumulado desde el inicio hasta la fecha actual.  
- **KPIs de Calidad de Datos**: como `% Ratio Devoluciones`, `Filas con Importe Negativo`, `Filas sin Cliente`.  

---

## 🛠️ Tecnologías y Herramientas Usadas

| Tecnología  | Propósito |
|-------------|-----------|
| Power BI    | Desarrollo de dashboards interactivos |
| DAX (Data Analysis Expressions) | Cálculo de KPIs avanzados |
| Power Query | Transformación y limpieza de datos |
| SQL (opcional) | Extracción y preprocesamiento de datos |
| Excel       | Validación de medidas (QA) |
| Tabular Editor | Organización del modelo y exportación de scripts |

---

## 📂 Estructura del Proyecto
```bash
📁 Proyecto-Ventas-Clientes
│── 📂 report/           # Informes principales en Power BI (.pbix / .pbit)
│── 📂 model/            # Modelo tabular y medidas (measures.dax / measures.md)
│── 📂 docs/             # Documentación, diccionario de datos, esquema relacional
│── 📂 tests/            # Archivos de QA de medidas
│── 📜 README.md         # Documentación principal
```

---

## 📌 Ejemplo de Código DAX

**Ventas Acumuladas Proyecto**  
```DAX
Total Ventas Acumuladas Proyecto =
CALCULATE (
    [Total Venta Neta],
    ALL (Dim_fechas), // Elimina los filtros de la tabla calendario
    Dim_fechas[Fecha_Key] <= MAX ( Dim_fechas[Fecha_Key]) // Suma todas las fechas hasta la actual
)
```

---

## 🚀 Cómo Usar Este Proyecto
1️⃣ Clonar el repositorio  
```bash
git clone https://github.com/tuusuario/ventas-clientes-bi.git
```  
2️⃣ Abrir el archivo `.pbix` en Power BI.  
3️⃣ Explorar el dashboard e interactuar con filtros.  
4️⃣ Revisar `measures.md` para entender los cálculos en detalle.  

---

## 🔮 Aprendizajes y Conclusiones
✔️ Entender análisis interanual (YoY) a nivel ejecutivo y operativo.  
✔️ Construir dashboards dinámicos en Power BI con bookmarks y storytelling.  
✔️ Escribir medidas DAX optimizadas para performance de negocio.  
✔️ Aplicar buenas prácticas en modelado de datos y validación de KPIs.  

---

## 🎯 Audiencia Objetivo
✅ **Analistas de Datos y Profesionales BI** – aprender técnicas avanzadas en Power BI.  
✅ **Reclutadores y Managers** – evaluar competencias de BI en un caso práctico.  
✅ **Empresas y Clientes** – obtener insights sobre tendencias de ventas y decisiones basadas en datos.  

---

## 📢 Conectemos
🔗 LinkedIn: [Tu perfil]  
📧 Email: [Tu email]  

🙌 Si este proyecto te resulta útil, ¡no olvides dejar una ⭐ en GitHub! 🚀

---

## 📌 Reflexión Final
Este proyecto conecta la visión **estratégica** y la **operativa**, mostrando cómo un dashboard avanzado en Power BI puede **impulsar decisiones de negocio basadas en datos**.  
¡Espero tu feedback y contribuciones! 💡🔥
