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

Como primer paso para la recoleccion de los datos importamos las librerias que utilizaremos en este proyecto de web scraping 
```Python
import requests
from bs4 import BeautifulSoup
import pandas as pd
```
luego creamos una funcion recopila información sobre autos de un año específico de múltiples páginas en Mercado Libre, usando web scraping, y devuelve listas con los modelos, años, precios y monedas de los autos encontrados.
```Python
def get_all_pages(starting_offset, increment, year):
    offset = starting_offset
    model_list = []
    year_list = []
    price_list = []
    currency_list = []

    while True:
        url = f"https://autos.mercadolibre.com.ar/{year}/_Desde_{offset}_NoIndex_True"
        response = requests.get(url)

        if response.status_code != 200:
            break

        soup = BeautifulSoup(response.content, 'html.parser')

        # lógica de scraping
        print(f"Scraping page with offset: {offset} for year: {year}")

        cars = soup.find_all("li", class_="ui-search-layout__item")

        for car in cars:
            model_element = car.find("h2", class_="ui-search-item__title")
            year_element = car.find("span", class_="andes-table__column--value")
            price_element = car.find("span", class_="andes-money-amount__fraction")
            currency_element = car.find("span", class_="andes-money-amount__currency-symbol")

            if model_element:
                model = model_element.text.strip()
            else:
                model = "No disponible"

            # Se asume que el año es el mismo que el parámetro de la URL
            year_value = year

            if price_element:
                price = price_element.text.strip()
            else:
                price = "No disponible"

            if currency_element:
                currency = currency_element.text.strip()
            else:
                currency = "No disponible"

            model_list.append(model)
            year_list.append(year_value)
            price_list.append(price)
            currency_list.append(currency)

        # Verificar si hay resultados en la página
        if not cars:
            break

        # Incrementar el offset para la siguiente página
        offset += increment

    return model_list, year_list, price_list, currency_list
```
Parámetros iniciales
```Python
starting_offset = 0
increment = 48
```
Inicializar listas globales para almacenar todos los datos
```Python
all_models = []
all_years = []
all_prices = []
all_currencies = []
```
Recorrer los años del 2003 al 2022
```Python
for year in range(2003, 2023):
    model_list, year_list, price_list, currency_list = get_all_pages(starting_offset, increment, year)
    all_models.extend(model_list)
    all_years.extend(year_list)
    all_prices.extend(price_list)
    all_currencies.extend(currency_list)
```
Crear el DataFrame con todos los datos
```Python
df = pd.DataFrame({
    "Modelo": all_models,
    "Año": all_years,
    "Moneda": all_currencies,
    "Precio": all_prices
})
```
Reordenar las columnas
```Python
df = df[["Modelo", "Año", "Moneda","Precio"]]
```
Guardar el DataFrame en un archivo CSV
```Python
df.to_csv("autos_2003_2022_mercado_libre.csv", index=False)
```
### Procesamiento y Normalización de Datos
- Formato Original de los Datos: CSV
- Software Utilizado: Microsoft Excel
- Tareas Realizadas:
    - Limpieza de datos (remover duplicados, manejar valores nulos).
    - Normalización de precios a pesos Argentinos (tipo de cambio oficial).
#### Para la normalizacion de los datos en Excel utilizamos la funcion Si de la siguiente manera
- =SI(C2="US$";D2*871;D2)
### Análisis de Datos en Power BI
- Importación de Datos: La base de datos normalizada fue importada a Power BI.
- Creación de Medidas en DAX: Se crearon funciones en DAX para calcular la depreciación promedio de los autos.
- Visualizaciones: Se generaron gráficos y tablas para mostrar la depreciación promedio de los autos por modelo.
### Las medidas creadas en Dax son las siguientes
para calcular el precio inicial
```yaml
Precio_Inicial = 
CALCULATE(
    AVERAGE(autos_2003_2022_mercado_libre[Precio normalizado]),
    FILTER(
        SUMMARIZE(
            autos_2003_2022_mercado_libre,
            autos_2003_2022_mercado_libre[modelo],
            "Num_Años", DISTINCTCOUNT(autos_2003_2022_mercado_libre[año])
        ),
        [Num_Años] >= 5
    ),
    FILTER(
        autos_2003_2022_mercado_libre,
        autos_2003_2022_mercado_libre[año] = CALCULATE(
            MIN(autos_2003_2022_mercado_libre[Año]),
            ALLEXCEPT(autos_2003_2022_mercado_libre, autos_2003_2022_mercado_libre[modelo])
        )
    )
)
```
funcion que calcula el precio promedio anual de cada modelo
```yaml
Precio_Promedio_Anual = 
CALCULATE(
    AVERAGE(autos_2003_2022_mercado_libre[Precio normalizado]),
    FILTER(
        SUMMARIZE(
            autos_2003_2022_mercado_libre,
            autos_2003_2022_mercado_libre[modelo],
            "Num_Años", DISTINCTCOUNT(autos_2003_2022_mercado_libre[año])
        ),
        [Num_Años] >= 5
    ),
    ALLEXCEPT(autos_2003_2022_mercado_libre, autos_2003_2022_mercado_libre[modelo], autos_2003_2022_mercado_libre[año])
)
```
Ahora debemos calcular la diferencia anual porcentual de cada modelo para asi encontrar que que menor tasa de debaluacion tenga 
```yaml
Diferencia_Anual_Porcentual = 
VAR PrecioInicial = [Precio_Inicial]
VAR PrecioPromedioAnual = [Precio_Promedio_Anual]
VAR Diferencia = DIVIDE(PrecioPromedioAnual - PrecioInicial, [Precio_Promedio_Anual]) * 100
RETURN 
IF(Diferencia > 0.1, Diferencia, BLANK())
```
### Los resultados arrojados por este analisis de los autos que menos se devaluan son los siguientes
| marca, modelo y versión        | porcentaje de devaluacion anual |
| -------------------------------| ------------------------------- | 
| Volkswagen Golf 1.6 Format     |                             0,12|              
| Toyota Etios 1.5 Xls           |                             0,35|
| Peugeot 2008 1.6 Feline        |                             0,37|
| Renault Captur 1.6 Intens Cvt  |                             0,59|
| Peugeot 2008 1.6 Thp Sport     |                             0,75|
