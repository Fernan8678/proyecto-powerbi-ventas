# 📐 Medidas en DAX – Proyecto Ventas & Clientes

Este documento lista todas las medidas utilizadas en el modelo, organizadas por temática.

---

## 💰 Ventas

### Total Ventas Acumuladas Proyecto
**Descripción:**  
Calcula las ventas netas acumuladas desde el inicio del proyecto hasta la fecha seleccionada, eliminando los filtros del calendario.  

**Fórmula DAX:**  
```DAX
Total Ventas Acumuladas Proyecto =
CALCULATE (
    [Total Venta Neta],
    ALL (Dim_fechas), // Elimina los filtros de la tabla calendario
    Dim_fechas[Fecha_Key]<= MAX ( Dim_fechas[Fecha_Key]) // Asegura que se sumen todas las fechas hasta la actual
)
```
