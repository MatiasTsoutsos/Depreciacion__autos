# Depreciación de los autos

Este proyecto analiza la devaluación de autos usados entre los años 2003 y 2022, utilizando datos obtenidos de Mercado Libre. A través de un proceso de web scraping, se recopilaron datos de autos, incluyendo el año, el precio y el modelo. Los datos fueron procesados y normalizados antes de ser analizados en Power BI para identificar los autos con menor depreciación.

### Objetivos del Proyecto

- Objetivo Principal: Identificar los autos con menor depreciación en el mercado de autos usados.

- Objetivos Específicos:
    - Obtener datos de autos usados de Mercado Libre mediante web scraping.
    - Procesar y normalizar los datos.
    - Analizar los datos en Power BI.
    - Generar visualizaciones y reportes que ayuden a entender la depreciación de los autos.

### Recolección de Datos
- Fuente de Datos: Mercado Libre
- Método de Recolección: Web scraping
- Tecnologías Utilizadas: Python (librerías: BeautifulSoup, pandas, Requests)

### Procesamiento y Normalización de Datos
- Formato Original de los Datos: CSV
- Software Utilizado: Microsoft Excel
- Tareas Realizadas:
    - Limpieza de datos (remover duplicados, manejar valores nulos).
    - Normalización de precios a pesos Argentinos (tipo de cambio oficial).

### Análisis de Datos en Power BI
- Importación de Datos: La base de datos normalizada fue importada a Power BI.
- Creación de Medidas en DAX: Se crearon funciones en DAX para calcular la depreciación promedio de los autos.
- Visualizaciones: Se generaron gráficos y tablas para mostrar la depreciación promedio de los autos por modelo.

## Resultados
### Autos con la Menor Depreciación
| Marca, Modelo y Versión        | Porcentaje de devaluacion anual |
| -------------------------------| ------------------------------- | 
| Volkswagen Golf 1.6 Format     |                             0,12|              
| Toyota Etios 1.5 Xls           |                             0,35|
| Peugeot 2008 1.6 Feline        |                             0,37|
| Renault Captur 1.6 Intens Cvt  |                             0,59|
| Peugeot 2008 1.6 Thp Sport     |                             0,75|
