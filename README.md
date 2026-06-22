# AdventureWorks Sales Analysis · Microsoft Fabric & Power BI

Proyecto final del módulo de Microsoft Fabric & Power BI del Máster en Business Intelligence & Data Analytics.

Análisis end-to-end de ventas de AdventureWorks (2017–2020): ingesta en Fabric, transformación con PySpark y visualización interactiva en Power BI.

---

## Stack tecnológico

![Microsoft Fabric](https://img.shields.io/badge/Microsoft_Fabric-0078D4?style=flat&logo=microsoft&logoColor=white)
![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![PySpark](https://img.shields.io/badge/PySpark-E25A1C?style=flat&logo=apachespark&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)

---

## Estructura del proyecto

```
adventureworks-powerbi-fabric-master-datascienceai/
│
├── README.md
├── notebook/
│   └── AdventureWorks_Silver_Transform.ipynb
├── powerbi/
│   └── AdventureWorks_ProyectoFinal/
│       ├── AdventureWorks_ProyectoFinal.pbip
│       ├── AdventureWorks_ProyectoFinal.Dataset/
│       └── AdventureWorks_ProyectoFinal.Report/
├── data/
│   └── bronze/
│       ├── FactSales.csv
│       ├── DimProduct.csv
│       ├── DimCustomer.csv
│       ├── DimDate.csv
│       ├── DimReseller.csv
│       ├── DimTerritory.csv
│       └── DimSalesOrder.csv
└── docs/
    └── ProyectoFinal_AdventureWorks.docx
```

---

## Arquitectura

```
CSV (raw) → Lakehouse Bronze → PySpark Notebook → Lakehouse Silver (Delta) → Power BI
```

**Arquitectura medallón en Microsoft Fabric:**
- **Bronze**: 7 CSVs cargados en `Files/bronze/` sin transformar
- **Silver**: 6 tablas Delta Lake limpias en `Tables/` listas para consumo

---

## Dataset

Fuente: [AdventureWorks Sales — Microsoft GitHub](https://github.com/microsoft/powerbi-desktop-samples)

| Tabla | Filas | Descripción |
|---|---|---|
| FactSales | 121.253 | Líneas de pedido de venta |
| DimProduct | 397 | Catálogo de productos |
| DimCustomer | 18.484 | Clientes directos |
| DimReseller | 701 | Distribuidores mayoristas |
| DimTerritory | 11 | Territorios de venta |
| DimDate | 1.461 | Calendario fiscal |
| DimSalesOrder | 121.253 | Canal y número de pedido |

---

## Transformaciones aplicadas (Power Query + PySpark)

- Eliminación de registros inválidos (CustomerKey = -1 y ResellerKey = -1)
- Conversión de claves de fecha `yyyyMMdd` a tipo `Date`
- Eliminación de duplicados por `SalesOrderLineKey`
- Renombrado y normalización de columnas
- Tipado correcto: `Currency.Type`, `Percentage.Type`, `Int64.Type`

---

## Modelo en estrella

6 relaciones Varios a uno (*:1) con `FactSales` como tabla de hechos central.

| Tabla origen (FactSales) | Columna clave | Tabla destino |
|---|---|---|
| FactSales | OrderDateKey → DateKey | DimDate |
| FactSales | ProductKey | DimProduct |
| FactSales | CustomerKey | DimCustomer |
| FactSales | ResellerKey | DimReseller |
| FactSales | SalesTerritoryKey | DimTerritory |
| FactSales | SalesOrderLineKey | DimSalesOrder |

---

## Medidas DAX principales

| Medida | Resultado |
|---|---|
| Total Ventas | 109,81 mill. € |
| Num Pedidos | 121.253 |
| Ticket Medio | 905,62 € |
| Margen Bruto | 12,55 mill. € |
| % Margen | 11,43% |
| Ventas Internet | 29,36 mill. (26,74%) |
| Ventas Reseller | 80,45 mill. (73,26%) |
| Ventas YTD | Acumulado anual por fecha |
| Ventas Año Anterior | Comparativa Year-over-Year |

---

## Informe Power BI — 4 páginas

| Página | Contenido |
|---|---|
| Resumen | KPIs globales, evolución mensual de ventas, ventas por categoría |
| Detalle | Tabla drill-down por producto, ventas por región |
| Canal | Comparativa Reseller vs Internet por categoría y evolución mensual |
| Geografía | Mapa ArcGIS, treemap por grupo geográfico, tabla por región |

**Interactividad:**
- Drillthrough desde Resumen → Detalle por categoría
- Navegación entre páginas con botones
- Filtros cruzados entre visuals
- Segmentaciones por FiscalYear y Channel

---

## Resultados clave

- **Bikes** es la categoría dominante con el **86,17%** de la facturación total
- El canal **Reseller** representa el **73,26%** de las ventas vs Internet (26,74%)
- **Southwest** es la región con mayor volumen de ventas (24,18 mill. €)
- **Australia** destaca por tener el mayor margen bruto relativo (**33,57%**)
- Tendencia creciente 2017→2019 con caída en 2020
