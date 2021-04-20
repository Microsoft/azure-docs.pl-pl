---
title: Synapse Studio notesów
description: Z tego artykułu dowiesz się, jak tworzyć i opracowywać notesy Azure Synapse Studio w celu przygotowywania i wizualizacji danych.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 6859a2f8571c11e6ef93a5e5b1635cdbe39ad001
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737674"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Tworzenie, opracowywanie i obsługa Synapse Studio notesów w Azure Synapse Analytics

Notes Synapse Studio to interfejs internetowy do tworzenia plików zawierających kod na żywo, wizualizacje i tekst narracji. Notesy są dobrym miejscem do weryfikowania pomysłów i używania szybkich eksperymentów w celu uzyskania szczegółowych informacji na temat danych. Notesy są również powszechnie używane w scenariuszach przygotowywania danych, wizualizacji danych, uczenia maszynowego i innych scenariuszy danych big data.

Notes Azure Synapse Studio umożliwia:

* Rozpoczynanie pracy bez nakładu pracy w zakresie konfiguracji.
* Zabezpieczanie danych za pomocą wbudowanych funkcji zabezpieczeń przedsiębiorstwa.
* Analizuj dane w formatach pierwotnych (CSV, txt, JSON itp.), formatach przetworzonych plików (parquet, Delta Lake, ORC itp.) oraz tabelarowych plikach danych SQL na platformach Spark i SQL.
* Wydajna wydajność dzięki ulepszonym możliwościom tworzenia i wbudowanej wizualizacji danych.

W tym artykule opisano sposób używania notesów w programie Azure Synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>Wersja zapoznawcza nowego notesu
Zespół usługi Synapse wprowadził nowy składnik notesów do usługi Synapse Studio, aby zapewnić spójne środowisko notesów dla klientów firmy Microsoft i zmaksymalizować możliwości odnajdywania, produktywności, udostępniania i współpracy. Nowe środowisko notesu jest gotowe do użycia w wersji zapoznawczej. Zaznacz przycisk **Funkcje w wersji zapoznawczej** na pasku narzędzi notesu, aby go włączyć. W poniższej tabeli przedstawiono porównanie funkcji istniejącego notesu (tak zwanego "notesu klasycznego") z nowym notesem w wersji zapoznawczej.  

|Cecha|Notes klasyczny|Notes w wersji zapoznawczej|
|--|--|--|
|%run| Nieobsługiwane | &#9745;|
|%history| Nieobsługiwane |&#9745;
|%load| Nieobsługiwane |&#9745;|
|%%html| Nieobsługiwane |&#9745;|
|Przeciąganie i upuszczanie w celu przeniesienia komórki| Nieobsługiwane |&#9745;|
|Formatowanie komórki tekstowej przy użyciu przycisków paska narzędzi|&#9745;| Niedostępne |
|Operacja cofania komórki| &#9745;| Niedostępne |


## <a name="create-a-notebook"></a>Tworzenie notesu

Istnieją dwa sposoby tworzenia notesu. Możesz utworzyć nowy notes lub zaimportować istniejący notes do obszaru roboczego Azure Synapse z witryny **Eksplorator obiektów.** Azure Synapse Studio rozpoznają standardowe Jupyter Notebook IPYNB.

![tworzenie notesu importu](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Tworzenie notesów

Notesy składają się z komórek, które są poszczególnymi blokami kodu lub tekstu, które mogą być niezależnie lub jako grupa.

### <a name="add-a-cell"></a>Dodawanie komórki

Istnieje wiele sposobów dodawania nowej komórki do notesu.

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

1. Rozwiń lewy górny przycisk **+ Komórka,** a następnie wybierz **pozycję Dodaj komórkę kodu lub** Dodaj **komórkę tekstową**.

    ![przycisk add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Umieść kursor na przestrzeni między dwiema komórkami i wybierz pozycję **Dodaj kod lub** Dodaj **tekst**.

    ![dodawanie komórki między spacjami](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Użyj [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisz A,** aby wstawić komórkę nad bieżącą komórką. Naciśnij **klawisz B,** aby wstawić komórkę poniżej bieżącej komórki.


# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

1. Rozwiń lewy górny przycisk **+ Komórka,** a następnie wybierz **komórkę kodu lub** **komórkę Markdown.**
    ![przycisk add-azure-notebook-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Wybierz znak plus na początku komórki, a następnie wybierz pozycję **Komórka kodu lub** Komórka **markdown.**

    ![add-azure-notebook-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Użyj [klawiszy skrótów aznb w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisz A,** aby wstawić komórkę nad bieżącą komórką. Naciśnij **klawisz B,** aby wstawić komórkę poniżej bieżącej komórki.

---

### <a name="set-a-primary-language"></a>Ustawianie języka podstawowego

Azure Synapse Studio obsługują cztery Apache Spark języków:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET dla Apache Spark (C#)

Język podstawowy dla nowych dodanych komórek można ustawić z listy rozwijanej na górnym pasku poleceń.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Korzystanie z wielu języków

W jednym notesie można używać wielu języków, określając poprawne polecenie magic języka na początku komórki. W poniższej tabeli wymieniono polecenia magic służące do przełączania języków komórek.

|Magic, polecenie |Język | Opis |  
|---|------|-----|
|%%pyspark| Python | Wykonaj zapytanie w **języku Python** w kontekście platformy Spark.  |
|%%spark| Scala | Wykonaj zapytanie **Scala** w kontekście platformy Spark.  |  
|%%sql| SparkSQL | Wykonaj zapytanie **SparkSQL** w kontekście platformy Spark.  |
|%%csharp | Platforma .NET dla platformy Spark C # | Wykonaj zapytanie **platformy .NET dla platformy Spark w języku C#** względem kontekstu platformy Spark. |

Na poniższej ilustracji przedstawiono przykładowy sposób pisania zapytania PySpark przy użyciu polecenia **magic %%pyspark** lub zapytania SparkSQL za pomocą polecenia **magic %%sql** w notesie **Spark(Scala).** Zwróć uwagę, że podstawowy język notesu jest ustawiony na pySpark.

   ![Polecenia magic platformy Synapse Spark](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages&quot;></a>Używanie tabel tymczasowych do odwoływać się do danych w różnych językach

Nie można odwoływać się do danych ani zmiennych bezpośrednio w różnych językach w notesie Synapse Studio notesie. Na platformie Spark tabela tymczasowa może być przywołyowana w różnych językach. Oto przykład sposobu odczytywania ramki danych w tabeli tymczasowej platformy Spark i używania jej `Scala` `PySpark` jako `SparkSQL` obejścia.

1. W komórce 1 odczytaj ramce danych z łącznika puli SQL przy użyciu języka Scala i utwórz tabelę tymczasową.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. W komórce 2 odpytuj dane przy użyciu języka Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. W komórce 3 użyj danych w PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense w stylu środowiska IDE

Azure Synapse Studio są zintegrowane z edytorem Monaco w celu doprowadzenia funkcji IntelliSense w stylu IDE do edytora komórek. Wyróżnianie składni, znacznik błędu i automatyczne uzupełnianie kodu ułatwiają pisanie kodu i szybsze identyfikowanie problemów.

Funkcje IntelliSense są na różnych poziomach dojrzałości dla różnych języków. Skorzystaj z poniższej tabeli, aby zobaczyć, co jest obsługiwane.

|Języki| Wyróżnianie składni | Znacznik błędu składni  | Uzupełnianie kodu składni | Uzupełnianie kodu zmiennej| Uzupełnianie kodu funkcji systemowej| Uzupełnianie kodu funkcji użytkownika| Inteligentne wcięcie | Składanie kodu|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Tak|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Spark (Scala)|Tak|Tak|Tak|Tak|-|-|-|Tak|
|SparkSQL|Tak|Tak|-|-|-|-|-|-|
|.NET for Spark (C#)|Tak|-|-|-|-|-|-|-|



### <a name="code-snippets"></a>Wstawki kodu

Notesy programu Azure Synapse Studio zawierają fragmenty kodu, które ułatwiają wprowadzanie typowych wzorców kodu, takich jak konfigurowanie sesji platformy Spark, odczytywanie danych jako ramki danych platformy Spark lub rysowanie wykresów za pomocą matplotlib itp.

Fragmenty kodu są wyświetlane w [funkcji IntelliSense](#ide-style-intellisense) w różnych sugestiach. Zawartość fragmentów kodu jest wyrównana z językiem komórki kodu. Możesz zobaczyć dostępne fragmenty kodu, wpisując fragment **kodu** lub dowolne słowa kluczowe wyświetlane w tytule fragmentu kodu w edytorze komórek kodu. Na przykład po wpisaniu **tekstu odczyt** można wyświetlić listę fragmentów kodu do odczytywania danych z różnych źródeł danych.

![Fragmenty kodu synapse](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)



### <a name="format-text-cell-with-toolbar-buttons"></a>Formatowanie komórki tekstowej z przyciskami paska narzędzi

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Możesz użyć przycisków formatowania na pasku narzędzi komórek tekstowych, aby wykonać typowe akcje w formacie markdown. Obejmuje ona pogrubienie tekstu, kursywę tekstu, wstawianie fragmentów kodu, wstawianie nieuporządkowanych list, wstawianie uporządkowanej listy i wstawianie obrazu z adresu URL.

  ![Pasek narzędzi komórki tekstowej synapse](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Pasek narzędzi przycisku Formatuj nie jest jeszcze dostępny dla notesu w wersji zapoznawczej. 

---

### <a name="undo-cell-operations"></a>Cofanie operacji na komórkach

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Wybierz przycisk **cofania** lub naciśnij **klawisze Ctrl+Z,** aby odwołać ostatnią operację komórki. Teraz możesz cofnąć maksymalnie 20 ostatnich historycznych akcji komórek. 

   ![Komórki cofania synapse](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Operacja cofania komórki nie jest jeszcze dostępna dla notesu w wersji zapoznawczej. 

---

### <a name="move-a-cell"></a>Przenoszenie komórki

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Wybierz wielokropek (...), aby uzyskać dostęp do menu akcji innych komórek po prawej stronie. Następnie wybierz **pozycję Przenieś komórkę w górę** lub Przenieś **komórkę w dół,** aby przenieść bieżącą komórkę. 

Możesz również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisze Ctrl+Alt+↑,** aby przenieść bieżącą komórkę w górę. Naciśnij **klawisze Ctrl+Alt+↓,** aby przenieść bieżącą komórkę w dół.

   ![przenoszenie komórki](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Kliknij lewą stronę komórki i przeciągnij ją do żądanej pozycji. 
    ![Komórki przenoszenia synapse](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Usuwanie komórki

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Aby usunąć komórkę, wybierz wielokropek (...), aby uzyskać dostęp do menu akcji innych komórek po prawej stronie, a następnie wybierz pozycję **Usuń komórkę**. 

Możesz również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisze D,D,** aby usunąć bieżącą komórkę.
  
   ![usuwanie komórki](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Aby usunąć komórkę, wybierz przycisk usuwania po prawej stronie komórki. 

Możesz również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisze Shift+D,** aby usunąć bieżącą komórkę. 

   ![azure-notebook-delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Zwijanie danych wejściowych komórki

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Wybierz przycisk strzałki w dolnej części bieżącej komórki, aby ją zwinąć. Aby ją rozwinąć, wybierz przycisk strzałki, gdy komórka jest zwinięta.

   ![zwiń wejście w komórce](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Wybierz **wielokropek** (...) Więcej poleceń na pasku narzędzi komórki i dane wejściowe, **aby** zwinąć dane wejściowe bieżącej komórki. Aby ją rozwinąć, wybierz **ukryte dane wejściowe,** gdy komórka jest zwinięta.

   ![azure-notebook-collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Zwinięcie danych wyjściowych komórki

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Wybierz przycisk **zwinięcia danych** wyjściowych w lewym górnym rogu bieżących danych wyjściowych komórki, aby je zwinąć. Aby ją rozwinąć, wybierz pozycję **Pokaż dane wyjściowe komórki,** gdy dane wyjściowe komórki są zwinięte.

   ![zwiń dane wyjściowe w komórce](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Wybierz **wielokropek** (...) Więcej poleceń na pasku narzędzi komórki i dane wyjściowe, **aby** zwinąć dane wyjściowe bieżącej komórki. Aby go rozwinąć, wybierz ten sam przycisk, gdy dane wyjściowe komórki są ukryte.

   ![azure-notebook-collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Uruchamianie notesów

Komórki kodu w notesie można uruchamiać pojedynczo lub wszystkie jednocześnie. Stan i postęp każdej komórki są reprezentowane w notesie.

### <a name="run-a-cell"></a>Uruchamianie komórki

Istnieje kilka sposobów uruchamiania kodu w komórce.

1. Umieść kursor na komórce, którą chcesz uruchomić, i wybierz przycisk **Run Cell** (Uruchom komórkę) lub naciśnij **klawisze Ctrl+Enter**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Użyj [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **klawisze Shift+Enter,** aby uruchomić bieżącą komórkę i wybrać komórkę poniżej. Naciśnij **klawisze Alt+Enter,** aby uruchomić bieżącą komórkę i wstawić nową komórkę poniżej.

---

### <a name="run-all-cells"></a>Uruchamianie wszystkich komórek
Wybierz przycisk **Uruchom wszystko,** aby uruchomić kolejno wszystkie komórki w bieżącym notesie.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Uruchom wszystkie komórki powyżej lub poniżej

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Aby uzyskać dostęp do menu akcji innych komórek po prawej stronie, wybierz wielokropek (**...**). Następnie wybierz pozycję **Uruchom komórki powyżej,** aby uruchomić wszystkie komórki powyżej bieżącej sekwencji. Wybierz **pozycję Uruchom komórki poniżej,** aby uruchomić wszystkie komórki poniżej bieżącej sekwencji.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Rozwiń listę rozwijaną z **przycisku Uruchom wszystko,** a następnie wybierz pozycję **Uruchom komórki powyżej,** aby uruchomić wszystkie komórki powyżej bieżącej sekwencji. Wybierz **pozycję Uruchom komórki poniżej,** aby uruchomić wszystkie komórki poniżej bieżącej sekwencji.

   ![azure-notebook-run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Anulowanie wszystkich uruchomionych komórek

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)
Wybierz przycisk **Anuluj wszystko,** aby anulować uruchomione komórki lub komórki oczekujące w kolejce. 
   ![anulowanie wszystkich komórek](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Wybierz przycisk **Anuluj wszystko,** aby anulować uruchomione komórki lub komórki oczekujące w kolejce. 
   ![azure-notebook-cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Informacje o notesach

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Nieobsługiwane.

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Możesz użyć polecenia ```%run <notebook path>``` magic, aby odwołać się do innego notesu w kontekście bieżącego notesu. Wszystkie zmienne zdefiniowane w notesie referencyjnym są dostępne w bieżącym notesie. ```%run``` Polecenie magic obsługuje wywołania zagnieżdżone, ale nie obsługuje wywołań cyklicznych. Otrzymasz wyjątek, jeśli głębokość instrukcji jest większa niż pięć. ```%run``` Polecenie aktualnie obsługuje tylko do przekazania ścieżki notesu jako parametru. 

Przykład: ``` %run /path/notebookA ```.

> [!NOTE]
> Odwołanie do notesu nie jest obsługiwane w potoku synapse.
>
>

---


### <a name="cell-status-indicator"></a>Wskaźnik stanu komórki

Stan wykonywania komórki krok po kroku jest wyświetlany pod komórką, aby ułatwić wyświetlanie bieżącego postępu. Po zakończeniu działania komórki zostanie wyświetlone podsumowanie wykonywania z łącznym czasem trwania i czasem zakończenia, które będzie przechowywane w przyszłości.

![stan komórki](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Wskaźnik postępu platformy Spark

Azure Synapse Studio jest wyłącznie oparta na uciece Spark. Komórki kodu są wykonywane zdalnie w puli Apache Spark serwera. Wskaźnik postępu zadania platformy Spark jest dostarczany z wyświetlonym pasek postępu w czasie rzeczywistym, który pomaga zrozumieć stan wykonywania zadania.
Liczba zadań na każde zadanie lub etap pomaga zidentyfikować poziom równoległy zadania platformy Spark. Możesz również przejść do szczegółów interfejsu użytkownika platformy Spark określonego zadania (lub etapu), wybierając link w nazwie zadania (lub etapu).


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Konfiguracja sesji platformy Spark

Możesz określić czas trwania limitu czasu, liczbę i rozmiar wykonawców, które mają zostać nadane bieżącej sesji platformy Spark, w **skrypcie Konfigurowanie sesji**. Aby zmiany konfiguracji zaszły, uruchom ponownie sesję platformy Spark. Wszystkie buforowane zmienne notesu zostaną wyczyszone.

[![zarządzanie sesjami](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Polecenie magic konfiguracji sesji platformy Spark
Ustawienia sesji platformy Spark można również określić za pomocą polecenia magic **%%configure.** Sesja platformy Spark wymaga ponownego uruchomienia w celu efektu ustawień. Zalecamy uruchomienie **%%configure** na początku notesu. Oto przykład, aby zapoznać się z https://github.com/cloudera/livy#request-body pełną listą prawidłowych parametrów 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```
> [!NOTE]
> Polecenie magic konfiguracji sesji platformy Spark nie jest obsługiwane w potoku synapse.
>
>

## <a name="bring-data-to-a-notebook"></a>Bring data to a notebook (Przyniesienie danych do notesu)

Dane można załadować z Azure Blob Storage, usługi Azure Data Lake Store Gen 2 i puli SQL, jak pokazano w poniższych przykładach kodu.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Odczytywanie pliku CSV z usługi Azure Data Lake Store Gen2 jako ramki danych platformy Spark

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Odczytywanie pliku CSV z Azure Blob Storage jako ramki danych platformy Spark

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Odczytywanie danych z podstawowego konta magazynu

Dostęp do danych można uzyskać bezpośrednio na podstawowym koncie magazynu. Nie ma potrzeby podania kluczy tajnych. W Eksplorator danych kliknij prawym przyciskiem myszy plik i wybierz pozycję **Nowy** notes, aby wyświetlić nowy notes z automatycznie wygenerowanym wyodrębniaczem danych.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Zapisywanie notesów

Możesz zapisać pojedynczy notes lub wszystkie notesy w obszarze roboczym.

1. Aby zapisać zmiany wprowadzone w jednym notesie, wybierz przycisk **Publikuj** na pasku poleceń notesu.

   ![publikowanie notesu](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Aby zapisać wszystkie notesy w obszarze roboczym, wybierz przycisk **Opublikuj wszystko** na pasku poleceń obszaru roboczego. 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

We właściwościach notesu można określić, czy podczas zapisywania mają być dołączane dane wyjściowe komórki.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Polecenia magiczne
W notesach programu Azure Synapse Studio można używać znanych poleceń magic jupyter. Przejrzyj następującą listę jako bieżące dostępne polecenia magic. Poinformuj [nas o swoich przypadkach użycia w usłudze GitHub,](https://github.com/MicrosoftDocs/azure-docs/issues/new) abyśmy w dalszym ciągu tworzyli więcej poleceń magicznych spełniających Twoje potrzeby.

> [!NOTE]
> W potoku usługi Synapse są obsługiwane tylko następujące polecenia magic: %%pyspark, %%spark, %%csharp, %%sql. 
>
>

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Dostępne polecenia magic wiersza: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Dostępne polecenia komórki: [%%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%%timeit,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%%capture,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture) [%%writefile,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile) [%%sql](#use-multiple-languages), [%%pyspark,](#use-multiple-languages) [%%spark,](#use-multiple-languages) [%%csharp,](#use-multiple-languages)[%%configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Dostępne polecenia magic wiersza: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%timeit,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%history,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history) [%run,](#notebook-reference) [%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Dostępne polecenia magic komórek: [%%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%%timeit,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%%capture,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture) [%%writefile,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile) [%%sql](#use-multiple-languages), [%%pyspark,](#use-multiple-languages) [%%spark,](#use-multiple-languages) [%%csharp,](#use-multiple-languages) [%%html,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html) [%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrowanie notesu

### <a name="add-a-notebook-to-a-pipeline"></a>Dodawanie notesu do potoku

Wybierz przycisk **Dodaj do potoku** w prawym górnym rogu, aby dodać notes do istniejącego potoku lub utworzyć nowy potok.

![Dodawanie notesu do potoku](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Wyznaczanie komórki parameters

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Aby sparametryzować notes, wybierz wielokropek (...), aby uzyskać dostęp do menu akcji innych komórek po prawej stronie. Następnie wybierz **pozycję Przełącz komórkę parametru,** aby wyznaczyć komórkę jako komórkę parameters.

![toggle-parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Aby sparametryzować notes, wybierz wielokropek (...), aby uzyskać dostęp do większej **liczby** poleceń na pasku narzędzi komórki. Następnie wybierz **pozycję Przełącz komórkę parametru,** aby wyznaczyć komórkę jako komórkę parameters.

![parametr azure-notebook-toggle-parameter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory szuka komórki parameters i traktuje tę komórkę jako wartości domyślne dla parametrów przekazanych w czasie wykonywania. Aparat wykonywania doda nową komórkę poniżej komórki parameters z parametrami wejściowymi, aby zastąpić wartości domyślne. Jeśli komórka parameters nie zostanie wyznaczona, wstrzyknięta komórka zostanie wstawiona w górnej części notesu.


### <a name="assign-parameters-values-from-a-pipeline"></a>Przypisywanie wartości parametrów z potoku

Po utworzeniu notesu z parametrami można go wykonać z potoku za pomocą Azure Synapse Notes. Po dodaniu działania do kanwy potoku będzie można ustawić  wartości parametrów w sekcji Parametry podstawowe na **karcie** Ustawienia. 

![Przypisywanie parametru](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Podczas przypisywania wartości parametrów można użyć języka wyrażeń [potoku lub](../../data-factory/control-flow-expression-language-functions.md) [zmiennych systemowych](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Klawisze skrótów

Podobnie jak w przypadku notesów Jupyter Notebooks, Azure Synapse Studio mają modalny interfejs użytkownika. Klawiatura robi różne rzeczy w zależności od trybu, w którym znajduje się komórka notesu. Synapse Studio notesy obsługują następujące dwa tryby dla danej komórki kodu: tryb polecenia i tryb edycji.

1. Komórka jest w trybie polecenia, gdy nie ma kursora tekstowego monitowania o wpisanie. Gdy komórka jest w trybie polecenia, możesz edytować notes jako całość, ale nie wpisywać tekstu w pojedynczych komórkach. Wprowadź tryb polecenia, naciskając lub używając myszy, aby wybrać `ESC` poza obszarem edytora komórki.

   ![tryb poleceń](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. Tryb edycji jest wskazywany przez kursor tekstowy z monitem o wpisanie w obszarze edytora. Gdy komórka jest w trybie edycji, możesz wpisać w komórce. Wprowadź tryb edycji, naciskając lub używając myszy, aby wybrać `Enter` pozycję w obszarze edytora komórki.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Klawisze skrótów w trybie polecenia

# <a name="classical-notebook"></a>[Notes klasyczny](#tab/classical)

Za pomocą następujących skrótów klawiszy można łatwiej nawigować i uruchamiać kod w Azure Synapse notesach.

| Akcja |Synapse Studio notesu  |
|--|--|
|Uruchom bieżącą komórkę i wybierz poniżej | Shift+Enter |
|Uruchom bieżącą komórkę i wstaw poniżej | Alt+Enter |
|Wybierz komórkę powyżej| W górę |
|Wybierz komórkę poniżej| W dół |
|Wstaw komórkę powyżej| A |
|Wstaw komórkę poniżej| B |
|Rozszerzanie zaznaczonych komórek powyżej| Shift+ W górę |
|Rozszerzanie zaznaczonych komórek poniżej| Shift + W dół|
|Przenieś komórkę w górę| Ctrl+Alt+↑ |
|Przenieś komórkę w dół| Ctrl+Alt+↓ |
|Usuwanie zaznaczonych komórek| D, D |
|Przełączanie do trybu edycji| Enter |

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

| Akcja |Synapse Studio notesu  |
|--|--|
|Uruchom bieżącą komórkę i wybierz poniżej | Shift+Enter |
|Uruchom bieżącą komórkę i wstaw poniżej | Alt+Enter |
|Uruchamianie bieżącej komórki| Ctrl+Enter |
|Wybierz komórkę powyżej| W górę |
|Wybierz komórkę poniżej| W dół |
|Wybieranie poprzedniej komórki| K |
|Wybierz następną komórkę| J |
|Wstaw komórkę powyżej| A |
|Wstaw komórkę poniżej| B |
|Usuwanie zaznaczonych komórek| Shift+D |
|Przełączanie do trybu edycji| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>Klawisze skrótów w trybie edycji


Za pomocą następujących skrótów klawiszy można łatwiej nawigować i uruchamiać kod Azure Synapse notesach w trybie edycji.

| Akcja |Synapse Studio notesów  |
|--|--|
|Przesuwanie kursora w górę | W górę |
|Przesuwanie kursora w dół|W dół|
|Cofnij|Ctrl + Z|
|Ponów|Ctrl + Y|
|Komentarz/komentarz|Ctrl + /|
|Usuń słowo przed|Ctrl + Backspace|
|Usuń wyraz po|Ctrl + Delete|
|Przejdź do rozpoczęcia komórki|Ctrl + Strona główna|
|Przejdź do końca komórki |Ctrl + koniec|
|Przejdź o jeden wyraz w lewo|Ctrl + lewo|
|Przejdź o jeden wyraz w prawo|Ctrl + w prawo|
|Zaznacz wszystko|Ctrl + A|
|Wcięcie| Ctrl +]|
|Dedent|Ctrl + [|
|Przełączanie do trybu polecenia| Esc |

---

## <a name="next-steps"></a>Następne kroki
- [Zapoznaj się z przykładami notesów synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Szybki start: tworzenie puli Apache Spark w Azure Synapse Analytics użyciu narzędzi internetowych](../quickstart-apache-spark-notebook.md)
- [Co to jest Apache Spark w programie Azure Synapse Analytics](apache-spark-overview.md)
- [Korzystanie z platformy .NET for Apache Spark z usługą Azure Synapse Analytics](spark-dotnet.md)
- [Dokumentacja programu .NET Apache Spark dla programu](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)