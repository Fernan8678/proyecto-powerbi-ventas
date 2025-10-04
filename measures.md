# Measures (documentación)

## ImporteDolares_Egresos

**Descripción:** Suma de ImporteDolares para comprobantes clasificados como 'Egresos por Ventas'.

**DAX:**

```dax
CALCULATE(
          SUM(Fact_ventas[ImporteDolares]),
          Fact_ventas[Clasificación Comprobante] = "Egresos por Ventas"
         )
```

---

## ImporteDolares_Ingresos

**Descripción:** Suma de ImporteDolares para comprobantes 'Ingresos por Ventas'.

**DAX:**

```dax
CALCULATE(
          SUM(Fact_ventas[ImporteDolares]),
          Fact_ventas[Clasificación Comprobante] = "Ingresos por Ventas"
          )
```

---

## ImportePesos_Egresos

**Descripción:** Suma de ImportePesos para comprobantes 'Egresos por Ventas'.

**DAX:**

```dax
CALCULATE(
          SUM(Fact_ventas[ImportePesos]),
          Fact_ventas[Clasificación Comprobante] = "Egresos por Ventas"
          )
```

---

## ImportePesos_Ingresos

**Descripción:** Suma de ImportePesos para comprobantes 'Ingresos por Ventas'.

**DAX:**

```dax
CALCULATE(
          SUM(Fact_ventas[ImportePesos]),
          Fact_ventas[Clasificación Comprobante] = "Ingresos por Ventas"
          )
```

---

## Promedio Ventas

**Descripción:** Promedio de [Total Venta Neta] por cada mes (por nombre de mes).

**DAX:**

```dax
VAR Promedio_ = 
    AVERAGEX(
             VALUES(Dim_fechas[Mes_nombre]),
             CALCULATE([Total Venta Neta])
             )
RETURN
COALESCE(Promedio_,0)
```

---

## Total Venta AC

**Descripción:** Acumulado anual (YTD) de [Total Venta Neta].

**DAX:**

```dax
TOTALYTD([Total Venta Neta],Dim_fechas[Fecha_Key])
```

---

## Total Venta AC LY

**Descripción:** Total Venta AC del año anterior (misma fecha del año previo).

**DAX:**

```dax
CALCULATE([Total Venta AC],SAMEPERIODLASTYEAR(Dim_fechas[Fecha_Key]))
```

---

## Total Venta Bruta

**Descripción:** Selecciona la moneda y devuelve el Importe bruto según la moneda seleccionada.

**DAX:**

```dax
VAR _Moneda = 
    SELECTEDVALUE(Dim_moneda[Moneda],"Peso") 
RETURN
SWITCH( _Moneda,
        "Peso",[ImportePesos_Ingresos],
        "Dolar",[ImporteDolares_Ingresos],
        BLANK()
        )
```

---

## Total Venta LY

**Descripción:** Total Venta Neta en el mismo periodo del año anterior.

**DAX:**

```dax
CALCULATE([Total Venta Neta],SAMEPERIODLASTYEAR(Dim_fechas[Fecha_Key]))
```

---

## Total Venta Neta

**Descripción:** Total Venta Bruta menos egresos según la moneda seleccionada; devuelve 0 si Total Venta Bruta es BLANK.

**DAX:**

```dax
VAR _Monedas = 
    SELECTEDVALUE(Dim_moneda[Moneda],"Peso") 
RETURN
SWITCH( _Monedas,
        "Peso", IF (
                    ISBLANK([Total Venta Bruta]),
                    0,
                    [Total Venta Bruta] - ABS([ImportePesos_Egresos])
                    ),
        "Dolar",IF (
                    ISBLANK([Total Venta Bruta]),
                    0,
                    [Total Venta Bruta] - ABS([ImporteDolares_Egresos])
                    ),
        BLANK()
        )
```

---

## Total Ventas MTD

**Descripción:** Total MTD de [Total Venta Neta].

**DAX:**

```dax
TOTALMTD([Total Venta Neta],Dim_fechas[Fecha_Key])
```

---

## Total Ventas QTD

**Descripción:** Total QTD de [Total Venta Neta].

**DAX:**

```dax
TOTALQTD([Total Venta Neta],Dim_fechas[Fecha_Key])
```

---

## Variación %

**Descripción:** Variación porcentual = Variación Absoluta / Total Venta LY.

**DAX:**

```dax
DIVIDE([Variación Absoluta],[Total Venta LY],0)
```

---

## Variación Absoluta

**Descripción:** Diferencia absoluta entre este periodo y LY.

**DAX:**

```dax
[Total Venta Neta] - [Total Venta LY]
```

---

## % Ratio Devoluciones

**Descripción:** Proporción de unidades devueltas sobre unidades vendidas.

**DAX:**

```dax
DIVIDE([Cantidad Devoluciones],[Unidades Vendidas],BLANK())
```

---

## Cantidad Devoluciones

**Descripción:** Suma de cantidades filtrando por comprobantes de tipo 'Devoluciones' y usando relación con producto.

**DAX:**

```dax
VAR Devo = 
    CALCULATE(
              SUM(Fact_ventas[Cantidad]),
              Fact_ventas[Clasificación Comprobante] = "Devoluciones",
              USERELATIONSHIP(Fact_ventas[Producto_Key], Dim_producto[Producto_Key])
             )
RETURN
COALESCE(Devo,0)
```

---

## Filas con Importe Negativo

**Descripción:** Cuenta filas donde ImportePesos <= 0.

**DAX:**

```dax
CALCULATE(
          COUNTROWS( Fact_ventas ),
          FILTER( Fact_ventas, Fact_ventas[ImportePesos] <= 0 )
          )
```

---

## Filas sin Cliente

**Descripción:** Cuenta filas donde Cliente_Key es BLANK.

**DAX:**

```dax
CALCULATE(
          COUNTROWS(Fact_ventas),
          FILTER(Fact_ventas, 
                 ISBLANK( Fact_ventas[Cliente_Key])
                 )
         )
```

---

## Filas totales

**Descripción:** Total de filas en la fact table.

**DAX:**

```dax
COUNTROWS(Fact_ventas)
```

---

## Total Egresos por Ventas

**Descripción:** Egresos absolutados según la moneda seleccionada.

**DAX:**

```dax
VAR _Moneda = 
    SELECTEDVALUE(Dim_moneda[Moneda],"Peso") 
RETURN
SWITCH( _Moneda,
        "Peso",ABS([ImportePesos_Egresos]),
        "Dolar",ABS([ImporteDolares_Egresos]),
        0
        )
```

---

## Unidades Vendidas

**Descripción:** Suma de Cantidad para comprobantes 'Ingresos por Ventas' y ajuste por tipo de producto si corresponde.

**DAX:**

```dax
VAR Unidades_ = 
    CALCULATE(
              SUM(Fact_ventas[Cantidad]),
              Fact_ventas[Clasificación Comprobante] = "Ingresos por Ventas"
              )
RETURN
IF (
    ISBLANK(Unidades_),
    0,
    CALCULATE(Unidades_,VALUES(Dim_producto[Tipo_Producto]))
    )
```

---

## Unidades Vendidas Prod

**Descripción:** Unidades vendidas usando relación con Dim_producto.

**DAX:**

```dax
CALCULATE([Unidades Vendidas],USERELATIONSHIP(Fact_ventas[Producto_Key], Dim_producto[Producto_Key]))
```

---

## Comprobantes Totales

**Descripción:** Cuenta comprobantes usando CROSSFILTER en ambas direcciones.

**DAX:**

```dax
VAR Comprobante_ = 
CALCULATE(
          COUNT(Dim_comprobante[Comprobante_Key]),
          CROSSFILTER(Dim_comprobante[Comprobante_Key],Fact_ventas[comprobante_key],Both)
          )
RETURN
COALESCE(Comprobante_,0)
```

---

## Titulo Venta por provincia

**Descripción:** Texto dinámico que muestra la provincia seleccionada y la cantidad de clientes.

**DAX:**

```dax
"VENTAS POR CIUDAD EN " & 
                            SELECTEDVALUE(Dim_geografica[Provincia]) &
                            " ("&[Clientes Totales]&" "&
                            IF([Clientes Totales]>1,"clientes)","cliente)")
```

---

## % Participación Producto

**Descripción:** Participación del producto dentro del total de ventas por producto.

**DAX:**

```dax
DIVIDE([Total Venta Producto],CALCULATE([Total Venta Producto],ALL(Dim_producto)),0)
```

---

## Dias Transcurridos UV Producto

**Descripción:** Calcula Dias Transcurridos UV usando userelationship con producto.

**DAX:**

```dax
CALCULATE([Dias Transcurridos UV],USERELATIONSHIP(Dim_producto[Producto_Key],Fact_ventas[Producto_Key]))
```

---

## Diferencia de posiciones ranking producto

**Descripción:** Diferencia entre ranking LY y ranking actual.

**DAX:**

```dax
[Ranking Productos LY]- [Ranking Productos]
```

---

## Participacion_Producto

**Descripción:** Alias de % participación por producto (similar a % Participación Producto).

**DAX:**

```dax
DIVIDE([Total Venta Producto],CALCULATE([Total Venta Producto],ALL(Dim_producto)),0)
```

---

## Promedio Venta Producto

**Descripción:** Promedio de venta entre productos que tuvieron al menos una venta.

**DAX:**

```dax
VAR ProductosConVenta =
    FILTER(
        VALUES(Dim_producto[Producto]),
        CALCULATE([Total Venta Producto]) > 0
    )
RETURN
IF(
    COUNTROWS(ProductosConVenta) = 0,
    BLANK(),
    AVERAGEX(ProductosConVenta, CALCULATE([Total Venta Producto]))
)
```

---

## Ranking Productos

**Descripción:** Ranking por Total Venta Producto (descendente, denso) dentro de la selección.

**DAX:**

```dax
IF(ISBLANK([Total Venta Producto]),BLANK(),RANKX(ALLSELECTED(Dim_producto[Tipo_Producto]),[Total Venta Producto],,DESC,Dense))
```

---

## Ranking Productos LY

**Descripción:** Ranking por Total Venta Producto LY (descendente, denso).

**DAX:**

```dax
IF(ISBLANK([Total Venta Producto]),BLANK(),RANKX(ALLSELECTED(Dim_producto[Tipo_Producto]),[Total Venta Producto LY],,DESC,Dense))
```

---

## Total Venta Producto

**Descripción:** Total Venta Neta usando la relación de producto activa.

**DAX:**

```dax
CALCULATE([Total Venta Neta],USERELATIONSHIP(Fact_ventas[Producto_Key],Dim_producto[Producto_Key]))
```

---

## Total Venta Producto AC

**Descripción:** Total Venta AC usando la relación de producto activa.

**DAX:**

```dax
CALCULATE([Total Venta AC],USERELATIONSHIP(Fact_ventas[Producto_Key],Dim_producto[Producto_Key]))
```

---

## Total Venta Producto LY

**Descripción:** Total Venta LY usando la relación de producto activa.

**DAX:**

```dax
CALCULATE([Total Venta LY],USERELATIONSHIP(Fact_ventas[Producto_Key],Dim_producto[Producto_Key]))
```

---

