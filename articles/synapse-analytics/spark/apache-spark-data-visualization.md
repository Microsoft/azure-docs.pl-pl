---
title: Wizualizacje
description: Wizualizowanie danych za pomocą notesów platformy Azure Synapse
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 523356947d6d5f93fa8ef2202ad6e7d235c6afdd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919786"
---
# <a name="visualize-data"></a>Wizualizowanie danych
Azure Synapse to zintegrowana usługa analityczna, która przyspiesza czas w celu wglądu w dane, między magazynami danych i systemami analizy danych Big Data. Wizualizacja danych to kluczowy składnik, który umożliwia uzyskanie wglądu w dane. Ułatwia to zrozumienie dużych i małych ilości danych. Ułatwia również wykrywanie wzorców, trendów i wartości odstających w grupach danych. 

W przypadku korzystania z Apache Spark w usłudze Azure Synapse Analytics istnieją różne wbudowane opcje ułatwiające wizualizację danych, w tym opcje wykresu notesu Synapse, dostęp do popularnych bibliotek Open Source i integrację z Synapse SQL i Power BI.

## <a name="notebook-chart-options"></a>Opcje wykresu notesu
W przypadku korzystania z notesu usługi Azure Synapse można przekształcić widok wyników tabelarycznych na dostosowany wykres przy użyciu opcji wykresu. W tym miejscu możesz wizualizować dane bez konieczności pisania kodu. 

### <a name="displaydf-function"></a>funkcja Display (DF)
```display```Funkcja umożliwia włączanie zapytań SQL i Apache Spark ramek dataframes oraz odporne do bogatych wizualizacji danych. ```display```Funkcja może być używana w ramkach dataframes lub odporne utworzonych w PySpark, Scala, Java i .NET.

Aby uzyskać dostęp do opcji wykresu:
1. Dane wyjściowe ```%%sql``` poleceń Magic są domyślnie wyświetlane w widoku renderowanej tabeli. Możesz również wywołać ```display(df)``` funkcję Spark Dataframes lub odporną na błędy rozproszone zestawy danych (RDD), aby utworzyć renderowany widok tabeli. 
   
2. Gdy masz renderowany widok tabeli, przełącz się do widoku wykresu.
   ![wbudowane wykresy](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. Teraz możesz dostosować wizualizację, określając następujące wartości:
   | Konfigurowanie | Opis |
   |--|--| 
   | Typ wykresu | ```display```Funkcja obsługuje szeroką gamę typów wykresów, w tym wykresy słupkowe, kreślenia punktowe, wykresy liniowe i inne |
   | Klucz | Określ zakres wartości dla osi x|
   | Wartość | Określ zakres wartości dla wartości osi y |
   | Grupa serii | Służy do określania grup dla agregacji | 
   | Agregacja | Metoda agregowania danych w wizualizacji| 
   
   
    > [!NOTE]
    > Domyślnie ```display(df)``` funkcja będzie przyjmować wykresy tylko z pierwszych 1000 wierszy danych. Sprawdź **agregację nad wszystkimi wynikami** i kliknij przycisk **Zastosuj** , aby zastosować generowanie wykresu z całego zestawu danych. Zadanie Spark zostanie wyzwolone po zmianie ustawienia wykresu. Należy pamiętać, że ukończenie obliczeń może potrwać kilka minut i renderować wykres.
   
4. Po wykonaniu tych czynności możesz wyświetlać i korzystać z ostatniej wizualizacji.

### <a name="displaydf-statistic-details"></a>Wyświetl szczegóły statystyczne (DF)
Można użyć <code>display(df, summary = true)</code> do sprawdzenia podsumowania statystyk danego Apache Spark Dataframe, która zawiera nazwę kolumny, typ kolumny, unikatowe wartości i brakujące wartości dla każdej kolumny. Możesz również wybrać opcję dla konkretnej kolumny, aby zobaczyć jej wartość minimalną, wartość maksymalną, wartość średnią i odchylenie standardowe.
    ![wbudowane wykresy — podsumowanie](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML (DF) — opcja
Notesy usługi Azure Synapse Analytics obsługują grafikę HTML przy użyciu ```displayHTML``` funkcji.

Poniższy obraz przedstawia przykład tworzenia wizualizacji przy użyciu [D3.js](https://d3js.org/).

   ![D3-js — przykład](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Uruchom Poniższy kod, aby utworzyć wizualizację powyżej.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Popularne biblioteki
Po przejściu do wizualizacji danych środowisko Python oferuje wiele bibliotek grafów, które są spakowane z wieloma różnymi funkcjami. Domyślnie każda pula Apache Spark w usłudze Azure Synapse Analytics zawiera zestaw nadzorowanych i popularnych bibliotek typu "open source". Możesz również dodawać i zarządzać dodatkowymi bibliotekami & wersjami przy użyciu możliwości zarządzania bibliotekami usługi Azure Synapse Analytics. 

### <a name="bokeh"></a>Bokeh
Można renderować biblioteki HTML lub interaktywne, takie jak **bokeh**, przy użyciu ```displayHTML(df)``` . 

Na poniższej ilustracji przedstawiono przykład kreślenia symboli na mapie za pomocą **bokeh**.

   ![bokeh — przykład](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Uruchom następujący przykładowy kod, aby narysować Powyższy obraz.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
Można renderować standardowe Wykreślanie bibliotek, takich jak matplotlib, przy użyciu wbudowanych funkcji renderowania dla każdej biblioteki.

Poniższy obraz przedstawia przykład tworzenia wykresu słupkowego przy użyciu **matplotlib**.
   ![Przykład wykresu liniowego.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Uruchom następujący przykładowy kod, aby narysować Powyższy obraz.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>Dodatkowe biblioteki 
Poza tymi bibliotekami środowisko uruchomieniowe usługi Azure Synapse Analytics zawiera również następujący zestaw bibliotek, które są często używane do wizualizacji danych:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Biblioteka seaborn](https://seaborn.pydata.org/) 

Można odwiedzić [dokumentację](./spark/../apache-spark-version-support.md) środowiska uruchomieniowego usługi Azure Synapse Analytics, aby uzyskać najbardziej aktualne informacje o dostępnych bibliotekach i wersjach.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Nawiązywanie połączenia z usługą Power BI przy użyciu usługi Apache Spark & SQL na żądanie
Usługa Azure Synapse Analytics integruje się z Power BI umożliwiających inżynierom danych Tworzenie rozwiązań analitycznych.

Usługa Azure Synapse Analytics umożliwia różnym aparatom obliczeniowym obszarów roboczych udostępnianie baz danych i tabel między jej pulami (wersja zapoznawcza) i aparatem SQL na żądanie (wersja zapoznawcza). Korzystając z [modelu metadanych udostępnionych](https://docs.microsoft.com/azure/synapse-analytics/metadata/overview), można wykonywać zapytania dotyczące tabel Apache Spark przy użyciu programu SQL na żądanie. Po wykonaniu tych czynności możesz połączyć punkt końcowy na żądanie SQL, aby Power BI łatwo wykonywać zapytania dotyczące zsynchronizowanych tabel platformy Spark.


## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat konfigurowania łącznika Spark SQL DW: [Synapse SQL Connector](./spark/../synapse-spark-sql-pool-import-export.md)
- Wyświetlanie bibliotek domyślnych: [środowisko uruchomieniowe usługi Azure Synapse Analytics](../spark/apache-spark-version-support.md)