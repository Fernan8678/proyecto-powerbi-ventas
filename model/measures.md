# 📐 Medidas en DAX – Proyecto Ventas & Clientes
Este documento lista todas las medidas utilizadas en el modelo, organizadas por temática.
---
## 💰 Ventas
### ImporteDolares_Egresos
**Descripción:** Suma de importes en dólares para egresos por ventas.

```DAX
ImporteDolares_Egresos = 
CALCULATE(
    SUM(Fact_ventas[ImporteDolares]),
    Fact_ventas[Clasificación Comprobante] = "Egresos por Ventas"
)
```

### ImporteDolares_Ingresos
**Descripción:** Suma de importes en dólares para ingresos por ventas.

```DAX
ImporteDolares_Ingresos = 
CALCULATE(
    SUM(Fact_ventas[ImporteDolares]),
    Fact_ventas[Clasificación Comprobante] = "Ingresos por Ventas"
)
```

### ImportePesos_Egresos
**Descripción:** Suma de importes en pesos para egresos por ventas.

```DAX
ImportePesos_Egresos = 
CALCULATE(
    SUM(Fact_ventas[ImportePesos]),
    Fact_ventas[Clasificación Comprobante] = "Egresos por Ventas"
)
```

### ImportePesos_Ingresos
**Descripción:** Suma de importes en pesos para ingresos por ventas.

```DAX
ImportePesos_Ingresos = 
CALCULATE(
    SUM(Fact_ventas[ImportePesos]),
    Fact_ventas[Clasificación Comprobante] = "Ingresos por Ventas"
)
```

### Total Venta Bruta
**Descripción:** Ventas brutas según la moneda seleccionada.

```DAX
Total Venta Bruta = 
VAR _Moneda = SELECTEDVALUE(Dim_moneda[Moneda],"Peso") 
RETURN
SWITCH(_Moneda,
    "Peso",[ImportePesos_Ingresos],
    "Dolar",[ImporteDolares_Ingresos],
    BLANK()
)
```

### Total Venta Neta
**Descripción:** Ventas netas después de egresos, según moneda.

```DAX
Total Venta Neta = 
VAR _Monedas = SELECTEDVALUE(Dim_moneda[Moneda],"Peso") 
RETURN
SWITCH(_Monedas,
    "Peso", IF(ISBLANK([Total Venta Bruta]),0,[Total Venta Bruta] - ABS([ImportePesos_Egresos])),
    "Dolar",IF(ISBLANK([Total Venta Bruta]),0,[Total Venta Bruta] - ABS([ImporteDolares_Egresos])),
    BLANK()
)
```

### Total Egresos por Ventas
**Descripción:** Total de egresos por ventas en la moneda seleccionada.

```DAX
Total Egresos por Ventas = 
VAR _Moneda = SELECTEDVALUE(Dim_moneda[Moneda],"Peso") 
RETURN
SWITCH(_Moneda,
    "Peso",ABS([ImportePesos_Egresos]),
    "Dolar",ABS([ImporteDolares_Egresos]),
    0
)
```

### Total Ventas Acumuladas Proyecto
**Descripción:** Ventas acumuladas desde inicio hasta la fecha actual.

```DAX
Total Ventas Acumuladas Proyecto = 
CALCULATE (
    [Total Venta Neta],
    ALL (Dim_fechas), 
    Dim_fechas[Fecha_Key]<= MAX ( Dim_fechas[Fecha_Key])
)
```

## 📈 KPIs de Ventas
### Total Venta AC
**Descripción:** Total ventas año actual.

```DAX
Total Venta AC = 
TOTALYTD([Total Venta Neta],Dim_fechas[Fecha_Key])
```

### Total Venta AC LY
**Descripción:** Total ventas año anterior acumulado.

```DAX
Total Venta AC LY = 
CALCULATE([Total Venta AC],SAMEPERIODLASTYEAR(Dim_fechas[Fecha_Key]))
```

### Total Venta LY
**Descripción:** Ventas netas año anterior.

```DAX
Total Venta LY = 
CALCULATE([Total Venta Neta],SAMEPERIODLASTYEAR(Dim_fechas[Fecha_Key]))
```

### Total Ventas MTD
**Descripción:** Ventas acumuladas mes actual.

```DAX
Total Ventas MTD = 
TOTALMTD([Total Venta Neta],Dim_fechas[Fecha_Key])
```

### Total Ventas QTD
**Descripción:** Ventas acumuladas trimestre actual.

```DAX
Total Ventas QTD = 
TOTALQTD([Total Venta Neta],Dim_fechas[Fecha_Key])
```

### Variación Absoluta
**Descripción:** Diferencia de ventas vs año anterior.

```DAX
Variación Absoluta = 
[Total Venta Neta] - [Total Venta LY]
```

### Variación %
**Descripción:** Variación porcentual de ventas netas.

```DAX
Variación % = 
DIVIDE([Variación Absoluta],[Total Venta LY],0)
```

### Promedio Ventas
**Descripción:** Promedio de ventas netas por mes.

```DAX
Promedio Ventas = 
VAR Promedio_ = AVERAGEX(VALUES(Dim_fechas[Mes_nombre]), CALCULATE([Total Venta Neta]))
RETURN COALESCE(Promedio_,0)
```

## 📦 Productos
### Unidades Vendidas
**Descripción:** Cantidad total de unidades vendidas.

```DAX
Unidades Vendidas = 
VAR Unidades_ = CALCULATE(SUM(Fact_ventas[Cantidad]), Fact_ventas[Clasificación Comprobante] = "Ingresos por Ventas")
RETURN IF(ISBLANK(Unidades_),0,CALCULATE(Unidades_,VALUES(Dim_producto[Tipo_Producto])))
```

### Unidades Vendidas Prod
**Descripción:** Unidades vendidas por producto.

```DAX
Unidades Vendidas Prod = 
CALCULATE([Unidades Vendidas], USERELATIONSHIP(Fact_ventas[Producto_Key], Dim_producto[Producto_Key]))
```

### % Participación Producto
**Descripción:** Porcentaje de participación de producto en total ventas.

```DAX
% Participación Producto = 
DIVIDE([Total Venta Producto], CALCULATE([Total Venta Producto],ALL(Dim_producto)),0)
```

### Participacion_Producto
**Descripción:** Participación duplicada para validación.

```DAX
Participacion_Producto = 
DIVIDE([Total Venta Producto], CALCULATE([Total Venta Producto],ALL(Dim_producto)),0)
```

### Promedio Venta Producto
**Descripción:** Promedio de venta para productos con ventas.

```DAX
Promedio Venta Producto = 
VAR ProductosConVenta = FILTER(VALUES(Dim_producto[Producto]), CALCULATE([Total Venta Producto]) > 0)
RETURN IF(COUNTROWS(ProductosConVenta) = 0, BLANK(), AVERAGEX(ProductosConVenta, CALCULATE([Total Venta Producto])))
```

### Ranking Productos
**Descripción:** Ranking de productos por ventas.

```DAX
Ranking Productos = 
IF(ISBLANK([Total Venta Producto]), BLANK(), RANKX(ALLSELECTED(Dim_producto[Tipo_Producto]),[Total Venta Producto],,DESC,Dense))
```

### Ranking Productos LY
**Descripción:** Ranking productos año anterior.

```DAX
Ranking Productos LY = 
IF(ISBLANK([Total Venta Producto]), BLANK(), RANKX(ALLSELECTED(Dim_producto[Tipo_Producto]),[Total Venta Producto LY],,DESC,Dense))
```

### Diferencia de posiciones ranking producto
**Descripción:** Diferencia de ranking productos entre años.

```DAX
Diferencia de posiciones ranking producto = 
[Ranking Productos LY]- [Ranking Productos]
```

### Total Venta Producto
**Descripción:** Ventas netas por producto.

```DAX
Total Venta Producto = 
CALCULATE([Total Venta Neta], USERELATIONSHIP(Fact_ventas[Producto_Key],Dim_producto[Producto_Key]))
```

### Total Venta Producto AC
**Descripción:** Ventas acumuladas año actual por producto.

```DAX
Total Venta Producto AC = 
CALCULATE([Total Venta AC], USERELATIONSHIP(Fact_ventas[Producto_Key],Dim_producto[Producto_Key]))
```

### Total Venta Producto LY
**Descripción:** Ventas año anterior por producto.

```DAX
Total Venta Producto LY = 
CALCULATE([Total Venta LY], USERELATIONSHIP(Fact_ventas[Producto_Key],Dim_producto[Producto_Key]))
```

### Dias Transcurridos UV Producto
**Descripción:** Días desde última venta del producto.

```DAX
Dias Transcurridos UV Producto = 
CALCULATE([Dias Transcurridos UV],USERELATIONSHIP(Dim_producto[Producto_Key],Fact_ventas[Producto_Key]))
```

## 👥 Clientes
### Clientes Totales
**Descripción:** Cantidad de clientes totales con ventas.

```DAX
Clientes Totales = 
VAR Cliente_ = CALCULATE(COUNT(Dim_cliente[Cliente_Key]), CROSSFILTER(Dim_cliente[Cliente_Key],Fact_ventas[Cliente_Key],Both))
RETURN COALESCE(Cliente_,0)
```

### Ranking Clientes
**Descripción:** Ranking clientes por ventas netas.

```DAX
Ranking Clientes = 
IF(ISBLANK([Total Venta Neta]), BLANK(), RANKX(ALLSELECTED(Dim_cliente),[Total Venta Neta],,DESC,Dense))
```

### Ranking Clientes LY
**Descripción:** Ranking clientes año anterior.

```DAX
Ranking Clientes LY = 
IF(ISBLANK([Total Venta LY]), BLANK(), RANKX(ALLSELECTED(Dim_cliente),[Total Venta LY],,DESC,Dense))
```

### Cambio Ranking Clientes
**Descripción:** Cambio ranking clientes entre años.

```DAX
Cambio Ranking Clientes = 
[Ranking Clientes LY] - [Ranking Clientes]
```

### Diferencia de posiciones ranking
**Descripción:** Diferencia ranking clientes.

```DAX
Diferencia de posiciones ranking = 
[Ranking Clientes LY] - [Ranking Clientes]
```

### Ultima Compra
**Descripción:** Última fecha de compra por cliente.

```DAX
Ultima Compra = 
CALCULATE(MAX(Fact_ventas[Fecha_Key]), FILTER(Fact_ventas,Fact_ventas[Cliente_Key] = RELATED(Dim_cliente[Cliente_Key])))
```

### Dias Transcurridos UV
**Descripción:** Días transcurridos desde la última compra.

```DAX
Dias Transcurridos UV = 
DATEDIFF([Ultima Compra],TODAY(),DAY)
```

### Promedio de Venta por cliente
**Descripción:** Promedio de venta por cliente con al menos una venta.

```DAX
Promedio de Venta por cliente = 
VAR ClientesConVenta = FILTER(VALUES(Dim_cliente[Cliente_Key]), CALCULATE([Total Venta Neta]) > 0)
RETURN IF(COUNTROWS(ClientesConVenta)=0, BLANK(), AVERAGEX(ClientesConVenta, CALCULATE([Total Venta Neta])))
```

### Promedio Mensual Cliente
**Descripción:** Promedio mensual de ventas por cliente.

```DAX
Promedio Mensual Cliente = 
CALCULATE([Promedio de Venta por cliente],ALLSELECTED(Dim_fechas[Mes_nombre]))
```

### Mediana Venta por Cliente
**Descripción:** Mediana de ventas en clientes activos.

```DAX
Mediana Venta por Cliente = 
VAR ClientesConVenta = FILTER(VALUES(Dim_cliente[Cliente_Key]), CALCULATE([Total Venta Neta]) > 0)
RETURN IF(COUNTROWS(ClientesConVenta)=0, BLANK(), MEDIANX(ClientesConVenta, CALCULATE([Total Venta Neta])))
```

### Titulo Venta por provincia
**Descripción:** Título dinámico con provincia y clientes.

```DAX
Titulo Venta por provincia = 
"VENTAS POR CIUDAD EN " & SELECTEDVALUE(Dim_geografica[Provincia]) & " ("&[Clientes Totales]&" "& IF([Clientes Totales]>1,"clientes)","cliente)")
```

## 🔍 Control y Calidad de Datos
### Cantidad Devoluciones
**Descripción:** Cantidad total de devoluciones.

```DAX
Cantidad Devoluciones = 
VAR Devo = CALCULATE(SUM(Fact_ventas[Cantidad]), Fact_ventas[Clasificación Comprobante] = "Devoluciones", USERELATIONSHIP(Fact_ventas[Producto_Key], Dim_producto[Producto_Key]))
RETURN COALESCE(Devo,0)
```

### % Ratio Devoluciones
**Descripción:** Proporción de devoluciones sobre ventas.

```DAX
% Ratio Devoluciones = 
DIVIDE([Cantidad Devoluciones],[Unidades Vendidas],BLANK())
```

### Filas con Importe Negativo
**Descripción:** Registros con importe negativo.

```DAX
Filas con Importe Negativo = 
CALCULATE(COUNTROWS(Fact_ventas), FILTER(Fact_ventas, Fact_ventas[ImportePesos] <= 0))
```

### Filas sin Cliente
**Descripción:** Registros sin cliente asociado.

```DAX
Filas sin Cliente = 
CALCULATE(COUNTROWS(Fact_ventas), FILTER(Fact_ventas, ISBLANK(Fact_ventas[Cliente_Key])))
```

### Filas totales
**Descripción:** Número total de filas en Fact_ventas.

```DAX
Filas totales = 
COUNTROWS(Fact_ventas)
```

### Comprobantes Totales
**Descripción:** Cantidad total de comprobantes.

```DAX
Comprobantes Totales = 
VAR Comprobante_ = CALCULATE(COUNT(Dim_comprobante[Comprobante_Key]), CROSSFILTER(Dim_comprobante[Comprobante_Key],Fact_ventas[comprobante_key],Both))
RETURN COALESCE(Comprobante_,0)
```

