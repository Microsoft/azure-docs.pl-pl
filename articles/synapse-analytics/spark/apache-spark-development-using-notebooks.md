---
title: Notesy programu Synapse Studio
description: W tym artykule dowiesz się, jak tworzyć i opracowywać notesy usługi Azure Synapse Studio (wersja zapoznawcza) w celu przygotowania i wizualizacji danych.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 02f304af10ae1907326d3f77f318a058155a4c21
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738735"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>Tworzenie, opracowywanie i konserwowanie notesów Synapse Studio (wersja zapoznawcza) w usłudze Azure Synapse Analytics

Notes programu Synapse Studio (wersja zapoznawcza) to interfejs sieci Web służący do tworzenia plików, które zawierają kod na żywo, wizualizacje i tekst opisowy. Notesy są dobrym miejscem do weryfikowania pomysłów i używania szybkich eksperymentów w celu uzyskania szczegółowych informacji na podstawie danych. Notesy są również szeroko używane podczas przygotowywania danych, wizualizacji danych, uczenia maszynowego i innych scenariuszy danych Big Data.

Za pomocą notesu usługi Azure Synapse Studio można:

* Wprowadzenie do pracy z konfiguracją zero.
* Zachowaj bezpieczeństwo danych dzięki wbudowanym funkcjom zabezpieczeń przedsiębiorstwa.
* Analizuj dane w formatach RAW (CSV, txt, JSON itp.), przetwarzanych formatach plików (Parquet, Delta Lake, ORC itp.) i plikach danych tabelarycznych SQL dla platformy Spark i SQL.
* Wydajniej dzięki ulepszonym funkcjom tworzenia i wbudowanej wizualizacji danych.

W tym artykule opisano sposób korzystania z notesów w usłudze Azure Synapse Studio.

## <a name="create-a-notebook"></a>Tworzenie notesu

Istnieją dwa sposoby tworzenia notesu. Możesz utworzyć nowy Notes lub zaimportować istniejący Notes do obszaru roboczego usługi Azure Synapse z **Eksplorator obiektów** . Notesy usługi Azure Synapse Studio mogą rozpoznawać standardowe pliki IPYNB Jupyter Notebook.

![Tworzenie notesu importowania](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Tworzenie notesów

Notesy składają się z komórek, które są pojedynczymi blokami kodu lub tekstu, które mogą być uruchamiane niezależnie lub jako Grupa.

### <a name="add-a-cell"></a>Dodaj komórkę

Istnieje wiele sposobów dodawania nowej komórki do notesu.

1. Rozwiń górny lewy przycisk **+ komórkę** , a następnie wybierz pozycję **Dodaj komórkę kodu** lub **Dodaj komórkę tekstową** .

    ![przycisk dodawania komórek z komórką](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Umieść kursor nad przestrzenią między dwiema komórkami, a następnie wybierz pozycję **Dodaj kod** lub **Dodaj tekst** .

    ![Dodaj komórkę między spacjami](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Użyj [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij klawisz **,** aby wstawić komórkę powyżej bieżącej komórki. Naciśnij klawisz **B** , aby wstawić komórkę poniżej bieżącej komórki.

### <a name="set-a-primary-language"></a>Ustawianie języka podstawowego

Notesy usługi Azure Synapse Studio obsługują cztery języki Apache Spark:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET dla Apache Spark (C#)

Można ustawić język podstawowy dla nowych dodanych komórek z listy rozwijanej na górnym pasku poleceń.

   ![Default-Synapse-Language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Korzystanie z wielu języków

Możesz użyć wielu języków w jednym notesie, określając poprawne polecenie Language Magic na początku komórki. Poniższa tabela zawiera listę poleceń Magic do przełączania języków komórek.

|Magic — polecenie |Język | Opis |  
|---|------|-----|
|%% pyspark| Python | Wykonaj zapytanie w języku **Python** względem kontekstu platformy Spark.  |
|%% Spark| Scala | Wykonaj zapytanie **Scala** względem kontekstu Spark.  |  
|%% SQL| SparkSQL | Wykonaj zapytanie **SparkSQL** względem kontekstu Spark.  |
|%% CSharp | .NET dla platformy Spark C # | Wykonaj zapytanie **platformy .NET dla platformy Spark w języku C#** względem kontekstu platformy Spark. |

Na poniższej ilustracji przedstawiono przykład sposobu pisania zapytania PySpark przy użyciu polecenia **%% PySpark** Magic lub zapytania SparkSQL z poleceniem **%% SQL** Magic w notesie **Spark (Scala)** . Zwróć uwagę, że podstawowy język notesu jest ustawiony na pySpark.

   ![Synapse Spark — polecenia](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Używanie tabel tymczasowych do odwoływania się do danych w różnych językach

Nie można odwoływać się do danych ani zmiennych bezpośrednio w różnych językach w notesie programu Synapse Studio. W platformie Spark można odwoływać się do tabeli tymczasowej w różnych językach. Oto przykład sposobu odczytywania `Scala` ramki danych w `PySpark` i `SparkSQL` używania tabeli tymczasowej platformy Spark jako obejścia.

1. W komórce 1 Odczytaj element Dataframe z łącznika puli SQL przy użyciu Scala i Utwórz tabelę tymczasową.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. W komórce 2 zbadaj dane przy użyciu platformy Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. W komórce 3 Użyj danych z PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>Technologia IntelliSense w stylu IDE

Notesy usługi Azure Synapse Studio są zintegrowane z edytorem Monako w celu przełączenia funkcji IntelliSense w stylu IDE do edytora komórek. Wyróżnianie składni, znacznik błędów i automatyczne uzupełnianie kodu ułatwiają pisanie kodu i szybsze identyfikowanie problemów.

Funkcje IntelliSense są na różnych poziomach dojrzałości dla różnych języków. Skorzystaj z poniższej tabeli, aby zobaczyć, co jest obsługiwane.

|Języki| Wyróżnianie składni | Znacznik błędu składniowy  | Uzupełnianie kodu składni | Uzupełnianie kodu zmiennej| Uzupełnianie kodu funkcji systemu| Uzupełnianie kodu funkcji użytkownika| Inteligentne wcięcie | Składanie kodu|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Tak|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Spark (Scala)|Tak|Tak|Tak|Tak|-|-|-|Tak|
|SparkSQL|Tak|Tak|-|-|-|-|-|-|
|.NET dla platformy Spark (C#)|Tak|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formatowanie komórki tekstowej przy użyciu przycisków paska narzędzi

Za pomocą przycisków formatowania na pasku narzędzi komórki tekstowe można wykonywać typowe działania dotyczące promocji. Zawiera tekst pogrubiony, tekst italicizing, wstawianie fragmentów kodu, wstawianie listy nieuporządkowanej, wstawianie uporządkowanej listy i Wstawianie obrazu z adresu URL.

  ![Pasek narzędzi komórki tekstu Synapse](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Operacje cofania komórek
Wybierz przycisk **Cofnij** lub naciśnij **klawisze CTRL + z** , aby odwołać ostatnią operację komórki. Teraz można cofnąć maksymalnie 20 akcji w ramach komórki historycznej. 

   ![Synapse Cofaj komórki](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Przenoszenie komórki

Wybierz wielokropek (...), aby uzyskać dostęp do menu akcji dodatkowych po prawej stronie. Następnie wybierz pozycję **Przenieś komórkę w górę** lub **Przenieś komórkę w dół** , aby przenieść bieżącą komórkę. 

Możesz również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **kombinację klawiszy Ctrl + Alt + ↑** , aby przenieść bieżącą komórkę w górę. Naciśnij **kombinację klawiszy Ctrl + Alt + ↓** , aby przenieść bieżącą komórkę w dół.

   ![Przenieś jako komórkę](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Usuń komórkę

Aby usunąć komórkę, wybierz wielokropek (...), aby uzyskać dostęp do menu Akcje dodatkowe komórki, a następnie wybierz pozycję **Usuń komórkę** . 

Możesz również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **d, d,** aby usunąć bieżącą komórkę.
  
   ![delete-a-Cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Zwiń dane wejściowe komórki
Wybierz przycisk strzałki w dolnej części bieżącej komórki, aby go zwinąć. Aby ją rozwinąć, wybierz przycisk strzałki, gdy komórka jest zwinięta.

   ![Zwiń komórki wejściowe](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Zwiń dane wyjściowe komórki

Wybierz przycisk **Zwiń dane wyjściowe** w lewym górnym rogu bieżącej komórki wyjściowej, aby go zwinąć. Aby ją rozwinąć, zaznacz pole wyboru **Pokaż dane wyjściowe komórki** , gdy dane wyjściowe komórki są zwijane.

   ![Zwiń komórki](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Uruchamianie notesów

Komórki kodu w notesie można uruchamiać pojedynczo lub wszystkie jednocześnie. Stan i postęp każdej komórki są reprezentowane w notesie.

### <a name="run-a-cell"></a>Uruchom komórkę

Istnieje kilka sposobów uruchamiania kodu w komórce.

1. Umieść kursor na komórce, którą chcesz uruchomić, a następnie wybierz przycisk **Uruchom komórkę** lub naciśnij **klawisze CTRL + ENTER** .

   ![Run-Cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Aby uzyskać dostęp do menu akcji dodatkowych po prawej stronie, wybierz wielokropek ( **...** ). Następnie wybierz pozycję **Uruchom komórkę** .

   ![Run-Cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Użyj [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij klawisze **SHIFT + ENTER** , aby uruchomić bieżącą komórkę i wybierz komórkę poniżej. Naciśnij klawisze **ALT + ENTER** , aby uruchomić bieżącą komórkę i Wstaw nową komórkę poniżej.


### <a name="run-all-cells"></a>Uruchom wszystkie komórki
Wybierz przycisk **Uruchom wszystko** , aby uruchomić wszystkie komórki w bieżącym notesie w sekwencji.

   ![Run-All-Cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Uruchom wszystkie komórki powyżej lub poniżej

Aby uzyskać dostęp do menu akcji dodatkowych po prawej stronie, wybierz wielokropek ( **...** ). Następnie wybierz pozycję **Uruchom komórki powyżej** , aby uruchomić wszystkie komórki znajdujące się nad bieżącą sekwencją. Wybierz pozycję **Uruchom komórki poniżej** , aby uruchomić wszystkie komórki znajdujące się pod bieżącą sekwencją.

   ![Run-Cells-above-lub-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Anuluj wszystkie uruchomione komórki
Wybierz przycisk **Anuluj wszystko** , aby anulować uruchomione komórki lub komórki oczekujące w kolejce. 
   ![Anuluj — wszystkie komórki](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>Wskaźnik stanu komórki

Poniżej komórki zostanie wyświetlony stan wykonania komórki krok po kroku, aby ułatwić wyświetlenie bieżącego postępu. Po zakończeniu przebiegu komórki zostanie wyświetlone podsumowanie wykonywania z łącznym czasem trwania i zakończenia, które będą przechowywane w przyszłości.

![komórka-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Wskaźnik postępu platformy Spark

Notes usługi Azure Synapse Studio jest całkowicie oparty na platformie Spark. Komórki kodu są wykonywane zdalnie w puli Spark. Zostanie wyświetlony wskaźnik postępu zadania platformy Spark z paskiem postępu w czasie rzeczywistym, który pomoże zrozumieć stan wykonywania zadania.
Liczba zadań poszczególnych zadań lub etapów ułatwia identyfikowanie równoległego poziomu zadania platformy Spark. Możesz również przejść do szczegółów do interfejsu użytkownika Spark określonego zadania (lub etapu) przez wybranie linku do nazwy zadania (lub etapu).


![Spark-Progress-wskaźnik](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Konfiguracja sesji platformy Spark

Możesz określić czas trwania limitu czasu, liczbę i rozmiar wykonawców, który ma zostać przypisany do bieżącej sesji platformy Spark w obszarze **Konfigurowanie sesji** . Aby zmiany konfiguracji zaczęły obowiązywać, należy ponownie uruchomić sesję platformy Spark. Wszystkie buforowane zmienne notesu są wyczyszczone.

[![Zarządzanie sesjami](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

W panelu konfiguracji sesji Spark jest teraz dostępna zalecana sesja platformy Spark. Pulę platformy Spark można wybrać bezpośrednio z poziomu panelu konfiguracja sesji i zobaczyć, ile węzłów jest używanych i ile pozostałych wykonawców jest dostępnych. Te informacje mogą pomóc w określeniu odpowiedniego rozmiaru sesji zamiast modyfikowania jego wartości i z powrotem.

![sesja — zalecane](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>Przenoszenie danych do notesu

Dane można ładować z usługi Azure Blob Storage, Azure Data Lake Store Gen 2 i puli SQL, jak pokazano w poniższych przykładach kodu.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Odczytaj plik CSV z Azure Data Lake Store Gen2 jako element Dataframe platformy Spark

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Odczytaj plik CSV z usługi Azure Blob Storage jako element Dataframe platformy Spark

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Odczytaj dane z podstawowego konta magazynu

Dostęp do danych można uzyskać bezpośrednio na podstawowym koncie magazynu. Nie ma potrzeby dostarczania kluczy tajnych. W Eksplorator danych kliknij prawym przyciskiem myszy plik i wybierz pozycję **Nowy Notes** , aby wyświetlić nowy Notes z wygenerowanym automatycznie programem wyodrębniania danych.

![dane do komórki](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Wizualizowanie danych w notesie

### <a name="produce-rendered-table-view"></a>Generuj renderowany widok tabeli

Widok wyników tabelarycznych jest dostępny z opcją tworzenia wykresu słupkowego, wykresu liniowego, wykresu kołowego, wykresu punktowego i wykresu warstwowego. Możesz wizualizować dane bez konieczności pisania kodu. Wykresy można dostosować w **opcjach wykresu** . 

Dane wyjściowe poleceń Magic **%% SQL** są wyświetlane domyślnie w widoku renderowanej tabeli. Możesz wywoływać <code>display(df)</code> na platformie Spark Dataframes, Pandas Dataframes, list lub odporną funkcję rozproszonego zestawu danych (RDD), aby utworzyć renderowany widok tabeli.

   [![Wykresy wbudowane](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>Wizualizuj wbudowane wykresy z zestawu danych o dużej skali 

Domyślnie <code>display(df)</code> funkcja będzie przyjmować wykresy tylko z pierwszych 1000 wierszy danych. Sprawdź **agregację dla wszystkich wyników** i wybierz przycisk **Zastosuj** . zostanie zastosowana generacja wykresu z całego zestawu danych. Zadanie Spark zostanie wyzwolone w przypadku zmiany ustawienia wykresu, trwa wykonywanie obliczeń i renderowanie wykresu. 
    [![wbudowane — wykresy — agregacja — wszystko](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)



### <a name="visualize-data-statistic-information"></a>Wizualizacja informacji statystycznych dotyczących danych
Można użyć <code>display(df, summary = True)</code> do sprawdzenia podsumowania statystyk danej ramki danych platformy Spark, która zawiera nazwę kolumny, typ kolumny, unikatowe wartości i brakujące wartości dla każdej kolumny. Możesz również wybrać opcję dla konkretnej kolumny, aby zobaczyć jej wartość minimalną, wartość maksymalną, wartość średnią i odchylenie standardowe.
    [![wbudowane — wykresy — podsumowanie ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>Renderowanie bibliotek HTML lub interaktywnych

Możesz renderować kod HTML, w tym JavaScript, CSS, D3 lub biblioteki interaktywne, takie jak **bokeh** , przy użyciu **displayHTML ()** .

Na poniższej ilustracji przedstawiono przykład kreślenia symboli na mapie za pomocą **bokeh** .

   ![bokeh — przykład](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

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

## <a name="save-notebooks"></a>Zapisuj notesy

Możesz zapisać pojedynczy Notes lub wszystkie notesy w obszarze roboczym.

1. Aby zapisać zmiany wprowadzone w pojedynczym notesie, wybierz przycisk **Publikuj** na pasku poleceń notesu.

   ![Publikowanie — Notes](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Aby zapisać wszystkie notesy w obszarze roboczym, wybierz przycisk **Opublikuj wszystko** na pasku poleceń obszaru roboczego. 

   ![Publikuj — wszystko](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

We właściwościach notesu można określić, czy podczas zapisywania mają być uwzględniane dane wyjściowe komórki.

   ![Notes — właściwości](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic — polecenia
Możesz używać znanych poleceń Jupyter Magic w notesach usługi Azure Synapse Studio. Przejrzyj poniższą listę jako bieżące dostępne polecenia Magic. Przekaż nam [swoje przypadki użycia w serwisie GitHub](https://github.com/MicrosoftDocs/azure-docs/issues/new) , aby można było dalej tworzyć bardziej magicowe polecenia, aby zaspokoić Twoje potrzeby.

Dostępne magicznki wiersza: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% czasu](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% czasu](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Dostępne magicy komórki: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% CSharp](#use-multiple-languages)


## <a name="integrate-a-notebook"></a>Integrowanie notesu

### <a name="add-a-notebook-to-a-pipeline"></a>Dodawanie notesu do potoku

Wybierz przycisk **Dodaj do potoku** w prawym górnym rogu, aby dodać Notes do istniejącego potoku lub utworzyć nowy potok.

![Dodawanie notesu do potoku](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Wyznaczanie komórki parametrów

Aby Sparametryzuj Notes, wybierz wielokropek (...), aby uzyskać dostęp do menu akcji dodatkowych. Następnie wybierz pozycję **Przełącz komórkę parametru** , aby wyznaczyć komórkę jako komórkę Parameters.

![Przełącznik — parametr](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

Azure Data Factory szuka komórki Parameters i traktuje tę komórkę jako wartości domyślne parametrów przekazywania w czasie wykonywania. Aparat wykonywania doda nową komórkę poniżej komórki Parameters z parametrami wejściowymi w celu zastąpienia wartości domyślnych. Gdy komórka parametrów nie jest wyznaczono, wprowadzona komórka zostanie wstawiona w górnej części notesu.

### <a name="assign-parameters-values-from-a-pipeline"></a>Przypisywanie wartości parametrów z potoku

Po utworzeniu notesu z parametrami można wykonać go z potoku za pomocą działania notesu Azure Synapse. Po dodaniu działania do kanwy potoku będzie możliwe ustawienie wartości parametrów w sekcji **parametry podstawowe** na karcie **Ustawienia** . 

![Przypisz parametr](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Podczas przypisywania wartości parametrów można użyć [języka wyrażenia potoku](../../data-factory/control-flow-expression-language-functions.md) lub [zmiennych systemowych](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Klawisze skrótów

Podobnie jak w przypadku notesów Jupyter, notesy platformy Azure Synapse Studio mają interfejs użytkownika modalnego. Klawiatura wykonuje różne czynności w zależności od trybu, w którym znajduje się komórka notesu. Notesy programu Synapse Studio obsługują następujące dwa tryby dla danej komórki kodu: Tryb poleceń i tryb edycji.

1. Komórka jest w trybie polecenia, gdy nie ma kursora tekstu z monitem o wpisanie. Gdy komórka jest w trybie poleceń, można edytować Notes jako całość, ale nie do pojedynczych komórek. Przejdź do trybu polecenia, naciskając `ESC` lub używając myszy, aby zaznaczyć poza obszarem edytora komórki.

   ![tryb polecenia](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Tryb edycji jest wskazywany przez kursor tekstowy z monitem o wpisanie w obszarze edytora. Gdy komórka jest w trybie edycji, możesz wpisać ją w komórce. Przejdź do trybu edycji, naciskając `Enter` lub używając myszy, aby wybrać obszar edytora komórki.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Klawisze skrótów w trybie polecenia

Korzystając z następujących skrótów klawiaturowych, można łatwiej nawigować i uruchamiać kod w notesach usługi Azure Synapse.

| Akcja |Skróty notesu programu Synapse Studio  |
|--|--|
|Uruchom bieżącą komórkę i wybierz poniżej | Shift+Enter |
|Uruchom bieżącą komórkę i Wstaw poniżej | Alt+Enter |
|Zaznacz komórkę powyżej| W górę |
|Wybierz komórkę poniżej| W dół |
|Wstaw komórkę powyżej| A |
|Wstaw komórkę poniżej| B |
|Rozciągnij zaznaczone komórki powyżej| Shift + Strzałka w górę |
|Rozciągnij zaznaczone komórki poniżej| Shift + Strzałka w dół|
|Przenieś komórkę w górę| Ctrl + Alt + ↑ |
|Przenieś komórkę w dół| Ctrl + Alt + ↓ |
|Usuń zaznaczone komórki| D, D |
|Przełącz do trybu edycji| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>Klawisze skrótów w trybie edycji

Korzystając z następujących skrótów klawiaturowych, można łatwiej nawigować i uruchamiać kod w notesach usługi Azure Synapse w trybie edycji.

| Akcja |Skróty notesu programu Synapse Studio  |
|--|--|
|Przenieś kursor w górę | W górę |
|Przenieś kursor w dół|W dół|
|Cofnij|Ctrl + Z|
|Ponów|Ctrl + Y|
|Skomentuj/Usuń komentarz|Ctrl +/|
|Usuń słowo przed|Ctrl + Backspace|
|Usuń słowo po|CTRL + DELETE|
|Przejdź do początku komórki|Ctrl + Home|
|Przejdź do końca komórki |Ctrl + End|
|Przejdź o jedno słowo w lewo|Ctrl + w lewo|
|Przejdź o jedno słowo w prawo|Ctrl + w prawo|
|Zaznacz wszystko|Ctrl + A|
|Wyświetlane| Ctrl +]|
|Zmniejsz wcięcie|CTRL + [|
|Przełącz do trybu polecenia| Esc |

## <a name="next-steps"></a>Następne kroki
- [Zapoznaj się z przykładowymi notesami Synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Szybki Start: Tworzenie puli Apache Spark (wersja zapoznawcza) w usłudze Azure Synapse Analytics przy użyciu narzędzi sieci Web](../quickstart-apache-spark-notebook.md)
- [Co to jest Apache Spark w usłudze Azure Synapse Analytics](apache-spark-overview.md)
- [Korzystanie z platformy .NET dla Apache Spark przy użyciu usługi Azure Synapse Analytics](spark-dotnet.md)
- [Dokumentacja platformy .NET dla Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
