---
title: 'Samouczek: Tworzenie notesu w Azure Cosmos DB, aby analizować i wizualizować dane'
description: 'Samouczek: informacje na temat używania wbudowanych notesów Jupyter do importowania danych do Azure Cosmos DB, analizowania danych i wizualizacji wyników.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 9b2ef5ddb56e3d0422a2a876993ddda0bd97e4ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85961102"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Samouczek: Tworzenie notesu w Azure Cosmos DB, aby analizować i wizualizować dane

W tym artykule opisano, jak używać wbudowanych notesów Jupyter do importowania przykładowych danych detalicznych do Azure Cosmos DB. Zobaczysz, jak używać poleceń SQL i Azure Cosmos DB Magic do uruchamiania zapytań, analizowania danych i wizualizacji wyników.

## <a name="prerequisites"></a>Wymagania wstępne

* [Włącz obsługę notesów podczas tworzenia konta usługi Azure Cosmos](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Tworzenie zasobów i importowanie danych
 
W tej sekcji utworzysz bazę danych i kontener usługi Azure Cosmos, a następnie zaimportujesz dane detaliczne do kontenera.

1. Przejdź do konta usługi Azure Cosmos i Otwórz **Eksplorator danych.**

1. Przejdź do karty **notesy** , wybierz pozycję `…` dalej w obszarze **Moje notesy** i Utwórz **Nowy Notes**. Wybierz język **Python 3** jako domyślne jądro.

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="Tworzenie nowego notesu&quot;:::

1. Po utworzeniu nowego notesu można zmienić jego nazwę na podobną do **VisualizeRetailData. ipynb.**

1. Następnie utworzysz bazę danych o nazwie &quot;RetailDemo&quot; i kontener o nazwie &quot;WebsiteData" w celu przechowywania danych detalicznych. Możesz użyć/CartID jako klucza partycji. Skopiuj i wklej następujący kod do nowej komórki w notesie i uruchom go:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Aby uruchomić komórkę, zaznacz `Shift + Enter` lub zaznacz komórkę i wybierz opcję **Uruchom aktywną komórkę** na pasku nawigacyjnym Eksploratora danych.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="Tworzenie nowego notesu&quot;:::

1. Po utworzeniu nowego notesu można zmienić jego nazwę na podobną do **VisualizeRetailData. ipynb.**

1. Następnie utworzysz bazę danych o nazwie &quot;RetailDemo&quot; i kontener o nazwie &quot;WebsiteData":::

   Baza danych i kontener są tworzone na bieżącym koncie usługi Azure Cosmos. Obsługa kontenera ma 400 RU/s. Po utworzeniu bazy danych i kontenera zostaną wyświetlone następujące dane wyjściowe. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Możesz również odświeżyć kartę **dane** i wyświetlić nowo utworzone zasoby:

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="Tworzenie nowego notesu&quot;:::

1. Po utworzeniu nowego notesu można zmienić jego nazwę na podobną do **VisualizeRetailData. ipynb.**

1. Następnie utworzysz bazę danych o nazwie &quot;RetailDemo&quot; i kontener o nazwie &quot;WebsiteData"
    }
   ```

   Na potrzeby samouczka przykładowe dane sprzedaży detalicznej są przechowywane w usłudze Azure Blob Storage. Możesz zaimportować go do kontenera usługi Azure Cosmos, wklejając Poniższy kod w nowej komórce. Można potwierdzić, że dane zostały pomyślnie zaimportowane przez uruchomienie zapytania, aby wybrać liczbę elementów.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Po uruchomieniu poprzedniego zapytania zwraca następujące dane wyjściowe:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Pobieranie danych do ramki Dataframe

Przed uruchomieniem zapytań w celu przeanalizowania danych, można odczytać dane z kontenera do [Pandas Dataframe](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) na potrzeby analizy. Aby odczytać dane w ramce Dataframe, użyj następującego polecenia SQL Magic:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Aby dowiedzieć się więcej, zobacz [wbudowane polecenia i funkcje notesu w artykule Azure Cosmos DB](use-python-notebook-features-and-commands.md) . Zapytanie zostanie uruchomione `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c` . Wyniki zostaną zapisane w Pandas Dataframe o nazwie df_cosmos. Wklej następujące polecenie w nowej komórce notesu i uruchom je:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

W nowej komórce notesu uruchom następujący kod, aby odczytać pierwsze 10 elementów z danych wyjściowych:

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="Tworzenie nowego notesu&quot;:::

1. Po utworzeniu nowego notesu można zmienić jego nazwę na podobną do **VisualizeRetailData. ipynb.**

1. Następnie utworzysz bazę danych o nazwie &quot;RetailDemo&quot; i kontener o nazwie &quot;WebsiteData":::

## <a name="run-queries-and-analyze-your-data"></a>Uruchamianie zapytań i analizowanie danych

W tej sekcji zostaną uruchomione pewne zapytania dotyczące pobranych danych.

* **Zapytanie1:** Uruchom zapytanie Grupuj według w ramce Dataframe, aby uzyskać sumę łącznego przychodu sprzedaży dla każdego kraju/regionu i wyświetlić 5 elementów z wyników. W nowej komórce notesu uruchom następujący kod:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="Tworzenie nowego notesu&quot;:::

1. Po utworzeniu nowego notesu można zmienić jego nazwę na podobną do **VisualizeRetailData. ipynb.**

1. Następnie utworzysz bazę danych o nazwie &quot;RetailDemo&quot; i kontener o nazwie &quot;WebsiteData":::

* **Query2:** Aby uzyskać listę pięciu pierwszych zakupionych elementów, Otwórz nową komórkę notesu i uruchom następujący kod:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="Tworzenie nowego notesu&quot;:::

1. Po utworzeniu nowego notesu można zmienić jego nazwę na podobną do **VisualizeRetailData. ipynb.**

1. Następnie utworzysz bazę danych o nazwie &quot;RetailDemo&quot; i kontener o nazwie &quot;WebsiteData":::

## <a name="visualize-your-data"></a>Wizualizacja danych  

1. Teraz, gdy mamy nasze dane dotyczące przychodów z kontenera usługi Azure Cosmos, możesz wizualizować dane za pomocą wybranej biblioteki wizualizacji. W tym samouczku zostanie użyta Biblioteka bokeh. Otwórz nową komórkę notesu i uruchom następujący kod, aby zainstalować bibliotekę bokeh. Po spełnieniu wszystkich wymagań biblioteka zostanie zainstalowana.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Następnie przygotuj się, aby wykreślić dane na mapie. Dołącz dane w Azure Cosmos DB z informacjami o kraju/regionie znajdującymi się w magazynie obiektów blob platformy Azure i Konwertuj wynik na format GEOJSON. Skopiuj następujący kod do nowej komórki notesu i uruchom go.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Wizualizuj przychody sprzedaży z różnych krajów/regionów na mapie światowej, uruchamiając następujący kod w nowej komórce notesu:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   Dane wyjściowe wyświetlają mapę świata z różnymi kolorami. Kolor ciemniejszy jaśniejszy reprezentuje kraje/regiony z najwyższym przychodem do najniższego przychodu.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="Tworzenie nowego notesu&quot;:::

1. Po utworzeniu nowego notesu można zmienić jego nazwę na podobną do **VisualizeRetailData. ipynb.**

1. Następnie utworzysz bazę danych o nazwie &quot;RetailDemo&quot; i kontener o nazwie &quot;WebsiteData":::

1. Przyjrzyjmy się innemu przypadku wizualizacji danych. Kontener WebsiteData zawiera rekord użytkowników, którzy przeglądali element, dodani do swojego koszyka i kupili ten element. Przyjrzyjmy się współczynnikowi konwersji zakupionych elementów. Uruchom następujący kod w nowej komórce, aby wizualizować szybkość konwersji dla każdego elementu:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="Tworzenie nowego notesu&quot;:::

1. Po utworzeniu nowego notesu można zmienić jego nazwę na podobną do **VisualizeRetailData. ipynb.**

1. Następnie utworzysz bazę danych o nazwie &quot;RetailDemo&quot; i kontener o nazwie &quot;WebsiteData":::

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat poleceń notesu języka Python, zobacz [jak używać wbudowanych poleceń i funkcji notesu w artykule Azure Cosmos DB](use-python-notebook-features-and-commands.md) .
