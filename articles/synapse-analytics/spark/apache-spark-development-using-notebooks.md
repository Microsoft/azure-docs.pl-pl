---
title: Notesy programu Synapse Studio
description: W tym artykule dowiesz się, jak tworzyć i opracowywać notesy usługi Azure Synapse Studio w celu przygotowania i wizualizacji danych.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: d5ff3fb988a7e907308ccccc8d0900d45a0601c0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671603"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Tworzenie, opracowywanie i konserwowanie notesów Synapse Studio w usłudze Azure Synapse Analytics

Notes programu Synapse Studio to interfejs sieci Web służący do tworzenia plików, które zawierają kod na żywo, wizualizacje i tekst opisowy. Notesy są dobrym miejscem do weryfikowania pomysłów i używania szybkich eksperymentów w celu uzyskania szczegółowych informacji na podstawie danych. Notesy są również szeroko używane podczas przygotowywania danych, wizualizacji danych, uczenia maszynowego i innych scenariuszy danych Big Data.

Za pomocą notesu usługi Azure Synapse Studio można:

* Wprowadzenie do pracy z konfiguracją zero.
* Zachowaj bezpieczeństwo danych dzięki wbudowanym funkcjom zabezpieczeń przedsiębiorstwa.
* Analizuj dane w formatach RAW (CSV, txt, JSON itp.), przetwarzanych formatach plików (Parquet, Delta Lake, ORC itp.) i plikach danych tabelarycznych SQL dla platformy Spark i SQL.
* Wydajniej dzięki ulepszonym funkcjom tworzenia i wbudowanej wizualizacji danych.

W tym artykule opisano sposób korzystania z notesów w usłudze Azure Synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>Wersja zapoznawcza nowego środowiska notesu
Synapse zespół przyniesieł nowy składnik Notess do programu Synapse Studio w celu zapewnienia spójnego środowiska Notatnika dla klientów firmy Microsoft i zmaksymalizowania możliwości wykrywania, produktywności, udostępniania i współpracy. Nowe środowisko notesu jest gotowe do wersji zapoznawczej. Zaznacz przycisk **Podgląd funkcji** na pasku narzędzi notesu, aby go włączyć. W poniższej tabeli przedstawiono porównanie funkcji między istniejącym notesem (nazywanym "klasycznym notesem") i nową wersją zapoznawczą.  

|Cecha|Klasyczny Notes|Notes w wersji zapoznawczej|
|--|--|--|
|% uruchomienia| Nieobsługiwane | &#9745;|
|Historia%| Nieobsługiwane |&#9745;
|obciążenie (%)| Nieobsługiwane |&#9745;|
|%% HTML| Nieobsługiwane |&#9745;|
|Przeciągnij i upuść, aby przenieść komórkę| Nieobsługiwane |&#9745;|
|Dane wyjściowe wyświetlania trwałego ()|&#9745;| Niedostępne |
|Anuluj wszystko| &#9745;| Niedostępne|
|Uruchom wszystkie komórki powyżej|&#9745;| Niedostępne |
|Uruchom wszystkie komórki poniżej|&#9745;| Niedostępne |
|Formatowanie komórki tekstowej przy użyciu przycisków paska narzędzi|&#9745;| Niedostępne |
|Operacja cofnięcia komórki| &#9745;| Niedostępne |


## <a name="create-a-notebook"></a>Tworzenie notesu

Istnieją dwa sposoby tworzenia notesu. Możesz utworzyć nowy Notes lub zaimportować istniejący Notes do obszaru roboczego usługi Azure Synapse z **Eksplorator obiektów**. Notesy usługi Azure Synapse Studio mogą rozpoznawać standardowe pliki IPYNB Jupyter Notebook.

![Tworzenie notesu importowania](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Tworzenie notesów

Notesy składają się z komórek, które są pojedynczymi blokami kodu lub tekstu, które mogą być uruchamiane niezależnie lub jako Grupa.

### <a name="add-a-cell"></a>Dodaj komórkę

Istnieje wiele sposobów dodawania nowej komórki do notesu.

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

1. Rozwiń górny lewy przycisk **+ komórkę** , a następnie wybierz pozycję **Dodaj komórkę kodu** lub **Dodaj komórkę tekstową**.

    ![przycisk dodawania komórek z komórką](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Umieść kursor nad przestrzenią między dwiema komórkami, a następnie wybierz pozycję **Dodaj kod** lub **Dodaj tekst**.

    ![Dodaj komórkę między spacjami](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Użyj [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij klawisz **,** aby wstawić komórkę powyżej bieżącej komórki. Naciśnij klawisz **B** , aby wstawić komórkę poniżej bieżącej komórki.


# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

1. Rozwiń górny lewy przycisk **+ komórkę** i wybierz **komórkę kodu** lub **komórkę promocji**.
    ![Dodawanie-Azure-Notes-komórka-with-Cell-Button](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Wybierz znak plus na początku komórki i wybierz **komórkę kodu** lub **komórkę promocji**.

    ![Dodaj platformę Azure — Notes-odstępy między komórkami](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Użyj [klawiszy skrótów aznb w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij klawisz **,** aby wstawić komórkę powyżej bieżącej komórki. Naciśnij klawisz **B** , aby wstawić komórkę poniżej bieżącej komórki.

---

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

1. W komórce 1 Odczytaj ramkę danych z łącznika puli SQL przy użyciu Scala i Utwórz tabelę tymczasową.

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

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

Za pomocą przycisków formatowania na pasku narzędzi komórki tekstowe można wykonywać typowe działania dotyczące promocji. Zawiera tekst pogrubiony, tekst italicizing, wstawianie fragmentów kodu, wstawianie listy nieuporządkowanej, wstawianie uporządkowanej listy i Wstawianie obrazu z adresu URL.

  ![Pasek narzędzi komórki tekstu Synapse](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Pasek narzędzi przycisków formatowania nie jest jeszcze dostępny w przypadku notesu w wersji zapoznawczej. 

---

### <a name="undo-cell-operations"></a>Operacje cofania komórek

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

Wybierz przycisk **Cofnij** lub naciśnij **klawisze CTRL + z** , aby odwołać ostatnią operację komórki. Teraz można cofnąć maksymalnie 20 akcji w ramach komórki historycznej. 

   ![Synapse Cofaj komórki](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Operacja Undo Cell nie jest jeszcze dostępna dla notesu w wersji zapoznawczej. 

---

### <a name="move-a-cell"></a>Przenoszenie komórki

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

Wybierz wielokropek (...), aby uzyskać dostęp do menu akcji dodatkowych po prawej stronie. Następnie wybierz pozycję **Przenieś komórkę w górę** lub **Przenieś komórkę w dół** , aby przenieść bieżącą komórkę. 

Możesz również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **kombinację klawiszy Ctrl + Alt + ↑** , aby przenieść bieżącą komórkę w górę. Naciśnij **kombinację klawiszy Ctrl + Alt + ↓** , aby przenieść bieżącą komórkę w dół.

   ![Przenieś jako komórkę](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Kliknij lewą stronę komórki i przeciągnij ją do żądanego położenia. 
    ![Synapse Przenieś komórki](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Usuń komórkę

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

Aby usunąć komórkę, wybierz wielokropek (...), aby uzyskać dostęp do menu Akcje dodatkowe komórki, a następnie wybierz pozycję **Usuń komórkę**. 

Możesz również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij **d, d,** aby usunąć bieżącą komórkę.
  
   ![delete-a-Cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Aby usunąć komórkę, wybierz przycisk Usuń z prawej strony komórki. 

Możesz również użyć [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij klawisze **SHIFT + D** , aby usunąć bieżącą komórkę. 

   ![Azure — Notes — usuwanie i komórka](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Zwiń dane wejściowe komórki

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

Wybierz przycisk strzałki w dolnej części bieżącej komórki, aby go zwinąć. Aby ją rozwinąć, wybierz przycisk strzałki, gdy komórka jest zwinięta.

   ![Zwiń komórki wejściowe](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Wybierz pozycję **więcej poleceń** wielokropka (...) na pasku narzędzi i **danych wejściowych** komórki, aby zwinąć bieżące dane wejściowe komórki. Aby ją rozwinąć, zaznacz pole wyboru **ukryty** , gdy komórka jest zwinięta.

   ![Azure-Notes — zwijanie tekstu](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Zwiń dane wyjściowe komórki

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

Wybierz przycisk **Zwiń dane wyjściowe** w lewym górnym rogu bieżącej komórki wyjściowej, aby go zwinąć. Aby ją rozwinąć, zaznacz pole wyboru **Pokaż dane wyjściowe komórki** , gdy dane wyjściowe komórki są zwijane.

   ![Zwiń komórki](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Wybierz pozycję **więcej poleceń** wielokropka (...) na pasku narzędzi i **danych wyjściowych** komórki, aby zwinąć dane wyjściowe bieżącej komórki. Aby ją rozwinąć, zaznacz ten sam przycisk, gdy dane wyjściowe komórki są ukryte.

   ![Azure — Notes — Zwiń zawartość komórki](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Uruchamianie notesów

Komórki kodu w notesie można uruchamiać pojedynczo lub wszystkie jednocześnie. Stan i postęp każdej komórki są reprezentowane w notesie.

### <a name="run-a-cell"></a>Uruchom komórkę

Istnieje kilka sposobów uruchamiania kodu w komórce.

1. Umieść kursor na komórce, którą chcesz uruchomić, a następnie wybierz przycisk **Uruchom komórkę** lub naciśnij **klawisze CTRL + ENTER**.

   ![Run-Cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Użyj [klawiszy skrótów w trybie polecenia](#shortcut-keys-under-command-mode). Naciśnij klawisze **SHIFT + ENTER** , aby uruchomić bieżącą komórkę i wybierz komórkę poniżej. Naciśnij klawisze **ALT + ENTER** , aby uruchomić bieżącą komórkę i Wstaw nową komórkę poniżej.

---

### <a name="run-all-cells"></a>Uruchom wszystkie komórki
Wybierz przycisk **Uruchom wszystko** , aby uruchomić wszystkie komórki w bieżącym notesie w sekwencji.

   ![Run-All-Cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

### <a name="run-all-cells-above-or-below"></a>Uruchom wszystkie komórki powyżej lub poniżej

Aby uzyskać dostęp do menu akcji dodatkowych po prawej stronie, wybierz wielokropek (**...**). Następnie wybierz pozycję **Uruchom komórki powyżej** , aby uruchomić wszystkie komórki znajdujące się nad bieżącą sekwencją. Wybierz pozycję **Uruchom komórki poniżej** , aby uruchomić wszystkie komórki znajdujące się pod bieżącą sekwencją.

   ![Run-Cells-above-lub-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Anuluj wszystkie uruchomione komórki
Wybierz przycisk **Anuluj wszystko** , aby anulować uruchomione komórki lub komórki oczekujące w kolejce. 
   ![Anuluj — wszystkie komórki](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Anulowanie wszystkich uruchomionych komórek jest jeszcze niedostępne dla notesu w wersji zapoznawczej. 

---



### <a name="reference-notebook"></a>Notes referencyjny

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

Nieobsługiwane.

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Możesz użyć ```%run <notebook path>``` Magic polecenia, aby odwołać się do innego notesu w kontekście bieżącego notesu. Wszystkie zmienne zdefiniowane w notesie referencyjnym są dostępne w bieżącym notesie. ```%run``` Magic polecenie obsługuje wywołania zagnieżdżone, ale nie obsługuje wywołań cyklicznych. Jeśli głębokość instrukcji jest większa niż pięć, zostanie wyświetlony wyjątek. ```%run``` polecenie obecnie obsługuje tylko przekazywanie ścieżki notesu jako parametru. 

Przykład: ``` %run /path/notebookA ```.

---


### <a name="cell-status-indicator"></a>Wskaźnik stanu komórki

Poniżej komórki zostanie wyświetlony stan wykonania komórki krok po kroku, aby ułatwić wyświetlenie bieżącego postępu. Po zakończeniu przebiegu komórki zostanie wyświetlone podsumowanie wykonywania z łącznym czasem trwania i zakończenia, które będą przechowywane w przyszłości.

![komórka-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Wskaźnik postępu platformy Spark

Notes usługi Azure Synapse Studio jest całkowicie oparty na platformie Spark. Komórki kodu są wykonywane zdalnie Apache Spark puli bezserwerowej. Zostanie wyświetlony wskaźnik postępu zadania platformy Spark z paskiem postępu w czasie rzeczywistym, który pomoże zrozumieć stan wykonywania zadania.
Liczba zadań poszczególnych zadań lub etapów ułatwia identyfikowanie równoległego poziomu zadania platformy Spark. Możesz również przejść do szczegółów do interfejsu użytkownika Spark określonego zadania (lub etapu) przez wybranie linku do nazwy zadania (lub etapu).


![Spark-Progress-wskaźnik](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Konfiguracja sesji platformy Spark

Możesz określić czas trwania limitu czasu, liczbę i rozmiar wykonawców, który ma zostać przypisany do bieżącej sesji platformy Spark w obszarze **Konfigurowanie sesji**. Aby zmiany konfiguracji zaczęły obowiązywać, należy ponownie uruchomić sesję platformy Spark. Wszystkie buforowane zmienne notesu są wyczyszczone.

[![Zarządzanie sesjami](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Magiczna konfiguracja sesji Spark — polecenie
Możesz również określić ustawienia sesji platformy Spark za pomocą polecenia Magic **%% Configure**. Aby ustawienia działały, należy ponownie uruchomić sesję platformy Spark. Zalecamy uruchomienie **konfiguracji%%** na początku Twojego notesu. Oto przykład, zobacz, aby https://github.com/cloudera/livy#request-body uzyskać pełną listę prawidłowych parametrów 

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

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Odczytaj plik CSV z usługi Azure Blob Storage jako element Dataframe platformy Spark

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

### <a name="read-data-from-the-primary-storage-account"></a>Odczytaj dane z podstawowego konta magazynu

Dostęp do danych można uzyskać bezpośrednio na podstawowym koncie magazynu. Nie ma potrzeby dostarczania kluczy tajnych. W Eksplorator danych kliknij prawym przyciskiem myszy plik i wybierz pozycję **Nowy Notes** , aby wyświetlić nowy Notes z wygenerowanym automatycznie programem wyodrębniania danych.

![dane do komórki](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

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

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

Dostępne magicznki wiersza: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% czasu](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Dostępne magicy komórki: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages),% [% CSharp](#use-multiple-languages),[%% Configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Dostępne magicznki wiersza: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% czasu](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [% History](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [% Run](#reference-notebook), [% Load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Dostępne magicy komórki: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% CSharp](#use-multiple-languages), [%% HTML](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%% Configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrowanie notesu

### <a name="add-a-notebook-to-a-pipeline"></a>Dodawanie notesu do potoku

Wybierz przycisk **Dodaj do potoku** w prawym górnym rogu, aby dodać Notes do istniejącego potoku lub utworzyć nowy potok.

![Dodawanie notesu do potoku](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Wyznaczanie komórki parametrów

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

Aby Sparametryzuj Notes, wybierz wielokropek (...), aby uzyskać dostęp do menu akcji dodatkowych. Następnie wybierz pozycję **Przełącz komórkę parametru** , aby wyznaczyć komórkę jako komórkę Parameters.

![Przełącznik — parametr](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

Aby Sparametryzuj Notes, wybierz wielokropek (...), aby uzyskać dostęp do **kolejnych poleceń** na pasku narzędzi komórki. Następnie wybierz pozycję **Przełącz komórkę parametru** , aby wyznaczyć komórkę jako komórkę Parameters.

![Azure-Notes-przełącznik — parametr](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory szuka komórki Parameters i traktuje tę komórkę jako wartości domyślne parametrów przekazywania w czasie wykonywania. Aparat wykonywania doda nową komórkę poniżej komórki Parameters z parametrami wejściowymi w celu zastąpienia wartości domyślnych. Gdy komórka parametrów nie jest wyznaczono, wprowadzona komórka zostanie wstawiona w górnej części notesu.


### <a name="assign-parameters-values-from-a-pipeline"></a>Przypisywanie wartości parametrów z potoku

Po utworzeniu notesu z parametrami można wykonać go z potoku za pomocą działania notesu Azure Synapse. Po dodaniu działania do kanwy potoku będzie możliwe ustawienie wartości parametrów w sekcji **parametry podstawowe** na karcie **Ustawienia** . 

![Przypisz parametr](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Podczas przypisywania wartości parametrów można użyć [języka wyrażenia potoku](../../data-factory/control-flow-expression-language-functions.md) lub [zmiennych systemowych](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Klawisze skrótów

Podobnie jak w przypadku notesów Jupyter, notesy platformy Azure Synapse Studio mają interfejs użytkownika modalnego. Klawiatura wykonuje różne czynności w zależności od trybu, w którym znajduje się komórka notesu. Notesy programu Synapse Studio obsługują następujące dwa tryby dla danej komórki kodu: Tryb poleceń i tryb edycji.

1. Komórka jest w trybie polecenia, gdy nie ma kursora tekstu z monitem o wpisanie. Gdy komórka jest w trybie poleceń, można edytować Notes jako całość, ale nie do pojedynczych komórek. Przejdź do trybu polecenia, naciskając `ESC` lub używając myszy, aby zaznaczyć poza obszarem edytora komórki.

   ![tryb polecenia](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. Tryb edycji jest wskazywany przez kursor tekstowy z monitem o wpisanie w obszarze edytora. Gdy komórka jest w trybie edycji, możesz wpisać ją w komórce. Przejdź do trybu edycji, naciskając `Enter` lub używając myszy, aby wybrać obszar edytora komórki.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Klawisze skrótów w trybie polecenia

# <a name="classical-notebook"></a>[Klasyczny Notes](#tab/classical)

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

# <a name="preview-notebook"></a>[Notes w wersji zapoznawczej](#tab/preview)

| Akcja |Skróty notesu programu Synapse Studio  |
|--|--|
|Uruchom bieżącą komórkę i wybierz poniżej | Shift+Enter |
|Uruchom bieżącą komórkę i Wstaw poniżej | Alt+Enter |
|Uruchom bieżącą komórkę| Ctrl+Enter |
|Zaznacz komórkę powyżej| W górę |
|Wybierz komórkę poniżej| W dół |
|Zaznacz poprzednią komórkę| K |
|Wybierz następną komórkę| J |
|Wstaw komórkę powyżej| A |
|Wstaw komórkę poniżej| B |
|Usuń zaznaczone komórki| Shift + D |
|Przełącz do trybu edycji| Enter |

---

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

---

## <a name="next-steps"></a>Następne kroki
- [Zapoznaj się z przykładowymi notesami Synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Szybki Start: Tworzenie puli Apache Spark w usłudze Azure Synapse Analytics przy użyciu narzędzi sieci Web](../quickstart-apache-spark-notebook.md)
- [Co to jest Apache Spark w usłudze Azure Synapse Analytics](apache-spark-overview.md)
- [Korzystanie z platformy .NET for Apache Spark z usługą Azure Synapse Analytics](spark-dotnet.md)
- [Dokumentacja platformy .NET dla Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)