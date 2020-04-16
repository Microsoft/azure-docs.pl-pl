---
title: Tworzenie, tworzenie i obsługa notesów usługi Azure Synapse Studio (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak tworzyć i tworzyć notesy usługi Azure Synapse Studio (wersja zapoznawcza) w celu przygotowania i wizualizacji danych.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430229"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Tworzenie, tworzenie i obsługa notesów usługi Azure Synapse Studio (wersja zapoznawcza)

Notes usługi Azure Synapse Studio (wersja zapoznawcza) to interfejs sieci web do tworzenia plików zawierających kod na żywo, wizualizacje i tekst narracyjny. Notesy są dobrym miejscem do sprawdzania poprawności pomysłów i korzystania z szybkich eksperymentów, aby uzyskać szczegółowe informacje na podstawie danych. Notesy są również szeroko stosowane w przygotowywaniu danych, wizualizacji danych, uczeniu maszynowym i innych scenariuszach big data.

Za pomocą notesu usługi Azure Synapse Studio można:

* Zacznij z zerowym nakładem konfiguracyjnym.
* Zabezpieczanie danych dzięki wbudowanym funkcjom zabezpieczeń przedsiębiorstwa.
* Analizowanie danych w formatach nieprzetworzonych (CSV, txt, JSON itp.), przetworzonych formatach plików (parkiet, Delta Lake, ORC itp.) i tabelaricznych plików danych SQL względem spark i SQL.
* Bądź produktywny dzięki ulepszonym funkcjom tworzenia i wbudowanej wizualizacji danych.

W tym artykule opisano sposób używania notesów w usłudze Azure Synapse Studio.

## <a name="create-a-notebook"></a>Tworzenie notesu

Istnieją dwa sposoby tworzenia notesu. Można utworzyć nowy notes lub zaimportować istniejący notes do obszaru roboczego Usługi Azure Synapse z **Eksploratora obiektów**. Notesy usługi Azure Synapse Studio mogą rozpoznawać standardowe pliki IPYNB notebooka Jupyter.

![synapsy-tworzenie-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Tworzenie notesów

Notesy składają się z komórek, które są pojedynczymi blokami kodu lub tekstu, które można uruchomić niezależnie lub jako grupa.

### <a name="add-a-cell"></a>Dodawanie komórki

Istnieje wiele sposobów dodawania nowej komórki do notesu.

1. Rozwiń lewy górny **przycisk + Komórka** i wybierz pozycję Dodaj **komórkę kodu** lub Dodaj **komórkę tekstową**.

    ![przycisk dodaj komórkę z komórką](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Umieść wskaźnik myszy na przestrzeni między dwiema komórkami i wybierz pozycję **Dodaj kod** lub **Dodaj tekst**.

    ![dodawanie komórki między przestrzenią](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Użyj [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisz A,** aby wstawić komórkę nad bieżącą komórką. Naciśnij **klawisz B,** aby wstawić komórkę pod bieżącą komórką.

### <a name="set-a-primary-language"></a>Ustawianie języka podstawowego

Notesy usługi Azure Synapse Studio obsługują cztery języki iskier:

* pyspark (python)
* iskra (Scala)
* sparkSQL
* Spark.NET (C#)

Język podstawowy można ustawić dla nowych dodanych komórek z listy rozwijanej na górnym pasku poleceń.

   ![domyślny język synapsy](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Używanie wielu języków

Można użyć wielu języków w jednym notesie, określając poprawne polecenie magiczne języka na początku komórki. W poniższej tabeli wymieniono magiczne polecenia przełączania języków komórek.

|Magiczne polecenie |Język | Opis |  
|---|------|-----|
|%%pyspark| Python | Wykonywanie kwerendy **języka Python** względem kontekstu platformy Spark.  |
|%%iskra| Scala | Wykonywanie kwerendy **Scala** względem kontekstu platformy Spark.  |  
|%%sql| SparkSQL ( SparkSQL ) | Wykonaj kwerendę **SparkSQL** względem kontekstu platformy Spark.  |
|%%csharp | Spark.NET C # | Wykonaj **kwerendę Spark.NET C#** względem kontekstu platformy Spark. |

Poniższa ilustracja jest przykładem sposobu pisania kwerendy PySpark przy użyciu polecenia **%%pyspark** magic lub kwerendy SparkSQL z poleceniem **%%sql** magic w notesie **Spark(Scala).** Należy zauważyć, że podstawowy język notesu jest ustawiony na Scala.

   ![synapsa-iskra-magia](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Używanie tabel tymczasowych do odwoływania się do danych w różnych językach

W notesie Synapse Studio nie można odwoływać się do danych ani zmiennych bezpośrednio w różnych językach. W spark tabeli tymczasowej można odwoływać się w różnych językach. Oto przykład sposobu odczytywania `Scala` dataframe `PySpark` `SparkSQL` i przy użyciu tabeli tymczasowej platformy Spark jako obejście problemu.

1. W komórce 1 odczytuj moduł DataFrame z łącznika puli SQL przy użyciu scali i utwórz tabelę tymczasową.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. W komórce 2 kwerendy danych przy użyciu programu Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. W 3 komórki użyj danych w PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense w stylu IDE

Notesy usługi Azure Synapse Studio są zintegrowane z edytorem Monaco, aby wprowadzić intellisense w stylu IDE do edytora komórek. Podświetlenie składni, kreator błędów i automatyczne uzupełnianie kodu pomagają szybciej pisać kod i identyfikować problemy.

Funkcje IntelliSense są na różnych poziomach dojrzałości dla różnych języków. Skorzystaj z poniższej tabeli, aby zobaczyć, co jest obsługiwane.

|Języki| Wyróżnienie składni | Znacznik błędu składni  | Zakończenie kodu składni | Uzupełnianie kodu zmiennej| Zakończenie kodu funkcji systemu| Zakończenie kodu funkcji użytkownika| Inteligentne wcięcie | Składanie kodu|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Tak|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Iskra (Scala)|Tak|Tak|Tak|Tak|-|-|-|Tak|
|SparkSQL ( SparkSQL )|Tak|Tak|-|-|-|-|-|-|
|Spark.NET (C#)|Tak|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formatowanie komórki tekstowej za pomocą przycisków paska narzędzi

Za pomocą przycisków formatowania na pasku narzędzi komórek tekstowych można wykonywać typowe akcje oznaczania. Zawiera pogrubienie tekstu, kursywę tekstu, wstawianie fragmentów kodu, wstawianie nieurządzonej listy, wstawianie uporządkowanej listy i wstawianie obrazu z adresu URL.

  ![pasek narzędzi synapse-text-cell](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Cofanie operacji komórek
Kliknij przycisk **Cofnij** lub naciśnij klawisze **Ctrl+Z,** aby odwołać ostatnią operację komórki. Teraz możesz cofnąć do najnowszych 20 historycznych akcji komórek. 

   ![synapsy-cofnij komórki](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Przenoszenie komórki

Wybierz wielokropek (...) aby uzyskać dostęp do menu akcji dodatkowych komórek po prawej stronie. Następnie wybierz pozycję **Przenieś komórkę w górę** lub Przenieś **komórkę w dół,** aby przenieść bieżącą komórkę. 

Można również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisze Ctrl+Alt+↑,** aby przenieść bieżącą komórkę w górę. Naciśnij **klawisze Ctrl+Alt+↓,** aby przesunąć bieżącą komórkę w dół.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Usuwanie komórki

Aby usunąć komórkę, zaznacz elipsy (...) aby uzyskać dostęp do menu akcji dodatkowych komórek po prawej stronie, a następnie wybierz pozycję **Usuń komórkę**. 

Można również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisz D,D,** aby usunąć bieżącą komórkę.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Zwijanie danych wejściowych komórki
Kliknij przycisk strzałki u dołu bieżącej komórki, aby ją zwinąć. Aby ją rozwinąć, kliknij przycisk strzałki, gdy komórka jest zwinięta.

   ![zwijanie-komórka-wejście](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Zwijanie danych wyjściowych komórki

Kliknij przycisk **zwiń wyjście** w lewym górnym rogu bieżącego wyjścia komórki, aby go zwinąć. Aby ją rozwinąć, kliknij pozycję **Pokaż dane wyjściowe komórki,** gdy dane wyjściowe komórki są zwinięte.

   ![zwijanie komórki-wyjście](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Uruchamianie notesów

Komórki kodu w notesie można uruchamiać pojedynczo lub wszystkie naraz. Stan i postęp każdej komórki są reprezentowane w notesie.

### <a name="run-a-cell"></a>Uruchamianie komórki

Istnieje kilka sposobów uruchamiania kodu w komórce.

1. Umieść wskaźnik myszy na komórce, którą chcesz uruchomić, a następnie wybierz przycisk **Uruchom komórkę** lub naciśnij **klawisze Ctrl+Enter**.

   ![komórka run-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Aby uzyskać dostęp do menu dodatkowych akcji komórek po prawej stronie, wybierz elipsy (**...**). Następnie wybierz pozycję **Uruchom komórkę**.

   ![komórka run-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Użyj [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisze Shift+Enter,** aby uruchomić bieżącą komórkę, a następnie zaznacz komórkę poniżej. Naciśnij **klawisze Alt+Enter,** aby uruchomić bieżącą komórkę i wstawić nową komórkę poniżej.


### <a name="run-all-cells"></a>Uruchom wszystkie komórki
Kliknij przycisk **Uruchom wszystko,** aby po kolei uruchomić wszystkie komórki w bieżącym notesie.

   ![wszystkie komórki run](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Uruchom wszystkie komórki powyżej lub poniżej

Aby uzyskać dostęp do menu dodatkowych akcji komórek po prawej stronie, wybierz elipsy (**...**). Następnie wybierz pozycję **Uruchom komórki powyżej,** aby kolejno uruchomić wszystkie komórki nad prądem. Wybierz **pozycję Uruchom komórki poniżej,** aby kolejno uruchomić wszystkie komórki poniżej bieżącego.

   ![komórki run-powyżej lub poniżej](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Wskaźnik stanu komórki

Stan wykonywania komórki krok po kroku jest wyświetlany pod komórką, aby ułatwić wyświetlanie jej bieżącego postępu. Po zakończeniu uruchamiania komórki podsumowanie wykonania z całkowitym czasem trwania i czasem zakończenia są wyświetlane i przechowywane tam do wykorzystania w przyszłości.

![stan komórki](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Wskaźnik postępu iskierki

Notes usługi Azure Synapse Studio jest oparty wyłącznie na platformie Spark. Komórki kodu są wykonywane zdalnie w puli platformy Spark. Wskaźnik postępu zadania platformy Spark jest dostarczany z paskiem postępu w czasie rzeczywistym, który ułatwia zrozumienie stanu wykonania zadania.


![wskaźnik postępu iskrowego](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Config sesji Iskry

Można określić czas trwania limitu czasu, liczbę i rozmiar executorów, aby nadać bieżącej sesji Spark w **konfiguracji sesji**. Uruchom ponownie sesję platformy Spark, aby zmiany konfiguracji zostały wprowadzone. Wszystkie buforowane zmienne notesu są wyczyszczone.

![sesja-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Wprowadzanie danych do notesu

Można załadować dane z usługi Azure Blob Storage, Azure Data Lake Store Gen 2 i puli SQL, jak pokazano w przykładach kodu poniżej.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Odczytanie pliku CSV z usługi Azure Data Lake Store Gen2 jako elementu Spark DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Odczytanie pliku CSV z usługi Azure Blob Storage jako elementu Spark DataFrame

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

### <a name="read-data-from-the-primary-storage-account"></a>Odczytywanie danych z podstawowego konta magazynu

Dostęp do danych można uzyskać bezpośrednio na koncie magazynu podstawowego. Nie ma potrzeby podawania tajnych kluczy. W Eksploratorze danych kliknij prawym przyciskiem myszy plik i wybierz pozycję **Nowy notes,** aby wyświetlić nowy notes z automatyczniegenerowanym ekstraktorem danych.

![danych do komórki](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Wizualizacja danych w notesie

### <a name="display"></a>Display()

Widok wyników tabelaryjskich jest wyposażony w opcję utworzenia wykresu słupkowego, wykresu liniowego, wykresu kołowego, wykresu punktowego i wykresu warstwowego. Można wizualizować dane bez konieczności pisania kodu. Wykresy można dostosować w **opcji wykresu**. 

Dane wyjściowe **poleceń magic %%sql** są domyślnie wyświetlane w widoku renderowanego stołu. Można wywołać **display(`<DataFrame name>`)** na Spark DataFrames lub Resilient Distributed Datasets (RDD) funkcja do produkcji renderowanego widoku tabeli.

   ![wbudowane wykresy](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Można renderować biblioteki HTML lub interaktywne, takie jak **bokeh,** za pomocą **displayHTML()**.

Poniższy obraz jest przykładem kreślenia glifów na mapie za pomocą **bokeh**.

   ![bokeh-przykład](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Uruchom poniższy przykładowy kod, aby narysować obraz powyżej.

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

## <a name="save-notebooks"></a>Zapisywanie notesów

W obszarze roboczym można zapisać pojedynczy notes lub wszystkie notesy.

1. Aby zapisać zmiany wprowadzone w jednym notesie, wybierz przycisk **Publikuj** na pasku poleceń notesu.

   ![publikuj-notes](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Aby zapisać wszystkie notesy w obszarze roboczym, wybierz przycisk **Opublikuj wszystko** na pasku poleceń obszaru roboczego. 

   ![publikowanie wszystkich](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

We właściwościach notesu można skonfigurować, czy podczas zapisywania mają być uwzględniane dane wyjściowe komórki.

   ![właściwości notesu](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magiczne polecenia
W notesach usługi Azure Synapse Studio można używać znanych magicznych poleceń Jupyter. Sprawdź poniższą listę jako aktualne dostępne magiczne polecenia. Poinformuj nas o przypadkach użycia w usłudze GitHub, abyśmy mogli nadal tworzyć więcej magicznych poleceń, aby spełnić Twoje potrzeby.

Dostępne magie wierszy: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Dostępne magie komórek: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Klawisze skrótów

Podobnie jak w przypadku notesów Jupyter, notesy usługi Azure Synapse Studio mają modalny interfejs użytkownika. Klawiatura wykonuje różne czynności w zależności od trybu, w którym znajduje się komórka notebooka. Notesy Synapse Studio obsługują następujące dwa tryby dla danej komórki kodu: tryb poleceń i tryb edycji.

1. Komórka jest w trybie polecenia, gdy nie ma kursora tekstowego z monitem o wpisanie. Gdy komórka jest w trybie polecenia, można edytować notes jako całość, ale nie wpisywać do poszczególnych komórek. Wprowadź tryb poleceń, naciskając `ESC` lub używając myszy, aby kliknąć poza obszarem edytora komórki.

   ![tryb polecenia](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Tryb edycji jest wskazywany przez kursor tekstowy z prośbą o wpisanie tekstu w obszarze edytora. Gdy komórka jest w trybie edycji, nie można wpisać do komórki. Wejdź w `Enter` tryb edycji, naciskając lub klikając myszą, aby kliknąć obszar edytora komórki.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Klawisze skrótów w trybie polecenia

Za pomocą następujących skrótów klawiszy można łatwiej nawigować i uruchamiać kod w notesach usługi Azure Synapse.

| Akcja |Skróty notesu Synapse Studio  |
|--|--|
|Uruchom bieżącą komórkę i wybierz poniżej | Shift+Enter |
|Uruchom bieżącą komórkę i wstaw poniżej | Alt+Enter |
|Zaznacz komórkę powyżej| W górę |
|Zaznacz komórkę poniżej| W dół |
|Wstaw komórkę powyżej| A |
|Wstaw komórkę poniżej| B |
|Rozszerzanie zaznaczonych komórek powyżej| Shift+w górę |
|Rozszerzanie zaznaczonych komórek poniżej| Shift+w dół|
|Przenoszenie komórki w górę| Ctrl+Alt+↑ |
|Przenoszenie komórki w dół| Ctrl+Alt+↓ |
|Usuwanie zaznaczonych komórek| D, D |
|Przełączanie do trybu edycji| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>Klawisze skrótów w trybie edycji

Korzystając z następujących skrótów klawiszy, można łatwiej nawigować i uruchamiać kod w notesach usługi Azure Synapse w trybie edycji.

| Akcja |Skróty do notesu Synapse Studio  |
|--|--|
|Przesuwanie kursora w górę | W górę |
|Przenoszenie kursora w dół|W dół|
|Cofanie|Ctrl + Z|
|Ponów|Ctrl + Y|
|Komentarz/Odkomentowanie|Ctrl + /|
|Usuwanie wyrazu przed|Ctrl + Obszar wsteczny|
|Usuń słowo po|Ctrl + Usuń|
|Przejdź do początku komórki|Ctrl + Strona główna|
|Przejdź do końca komórki |Ctrl + Koniec|
|Przejdź o jedno słowo w lewo|Ctrl + w lewo|
|Przejdź o jedno słowo w prawo|Ctrl + prawy|
|Zaznacz wszystkie|Ctrl + A|
|Wcięcie| Ctrl + ]|
|Dedent ( Dedent )|Ctrl + [|
|Przełączanie do trybu poleceń| Esc |

## <a name="next-steps"></a>Następne kroki

- [.NET dla apache Spark dokumentacji](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
