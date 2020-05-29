---
title: 'Samouczek: Rozpoczynanie pracy z usługą Azure Synapse Analytics'
description: Kroki krok po kroku, aby szybko zrozumieć podstawowe koncepcje w usłudze Azure Synapse
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 75c8d52a750567d3b34ad2aea236477ca8c97245
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171416"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Wprowadzenie z usługą Azure Synapse Analytics

Ten samouczek przeprowadzi Cię przez wszystkie podstawowe kroki niezbędne do skonfigurowania usługi Azure Synapse Analytics i korzystania z niej.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Przygotowywanie konta magazynu do użycia z obszarem roboczym Synapse

* Otwórz [Azure Portal](https://portal.azure.com)
* Utwórz nowe konto magazynu z następującymi ustawieniami:
    |Tab|Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|---|
    |Podstawy|**Nazwa konta magazynu**| Możesz nadać mu dowolną nazwę.|W tym dokumencie odwołujemy się do niego jako `contosolake` .|
    |Podstawy|**Rodzaj konta**|Musi być ustawiona na`StorageV2`||
    |Podstawy|**Lokalizacja**|Możesz wybrać dowolną lokalizację| Zalecamy, aby obszar roboczy Synapse i konto Azure Data Lake Storage (ADLS) Gen2 są w tym samym regionie.|
    |Zaawansowany|**Data Lake Storage Gen2**|`Enabled`| Usługa Azure Synapse działa tylko z kontami magazynu, na których to ustawienie jest włączone.|

1. Po utworzeniu konta magazynu wybierz pozycję **Kontrola dostępu (IAM)** na lewym pasku nawigacyjnym. Następnie przypisz poniższe role lub upewnij się, że zostały już przypisane. 
    a. * Przypisz siebie do roli **właściciela** na koncie magazynu b. * Przypisz siebie do roli **właściciela danych obiektów blob magazynu** na koncie magazynu
1. W okienku nawigacji po lewej stronie wybierz pozycję **kontenery** i Utwórz kontener. Możesz nadać mu dowolną nazwę. Zaakceptuj domyślny **poziom dostępu publicznego**. W tym dokumencie wywołamy kontener `users` . Wybierz przycisk **Utwórz**. 

## <a name="create-a-synapse-workspace"></a>Tworzenie obszaru roboczego Synapse

* Otwórz [Azure Portal](https://portal.azure.com) i Znajdź na początku `Synapse` .
* W wynikach wyszukiwania w obszarze **usługi**wybierz pozycję **Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)**
* Wybierz pozycję **+ Dodaj** , aby utworzyć nowy obszar roboczy z tymi ustawieniami

    |Tab|Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|---|
    |Podstawy|**Nazwa obszaru roboczego**|Możesz wywoływać wszystko.| W tym dokumencie będziemy używać`myworkspace`|
    |Podstawy|**Region**|Dopasuj region konta magazynu|

1. W obszarze **wybierz Data Lake Storage Gen 2**wybierz wcześniej utworzone konto i kontener.
    > [!NOTE]
    > Odwołujemy się do konta magazynu wybranego w tym miejscu jako podstawowego konta magazynu obszaru roboczego Synapse. To konto jest używane do przechowywania danych w tabelach platformy Apache Spark oraz dla dzienników utworzonych podczas tworzenia pul platformy Spark lub uruchamiania aplikacji Spark.

1. Wybierz pozycję **Przegląd + utwórz**. Wybierz przycisk **Utwórz**. Obszar roboczy będzie gotowy w ciągu kilku minut.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Sprawdź, czy plik MSI obszaru roboczego Synapse ma dostęp do konta magazynu

Mogło to być już zrobione. W każdym przypadku należy zweryfikować.

1. Otwórz [Azure Portal](https://portal.azure.com) i Otwórz konto magazynu podstawowego wybrane dla Twojego obszaru roboczego.
1. Wybierz pozycję **Kontrola dostępu (IAM)** na lewym pasku nawigacyjnym. Następnie przypisz poniższe role lub upewnij się, że zostały już przypisane. 
    a. Przypisz tożsamość obszaru roboczego do roli **współautor danych obiektów blob magazynu** na koncie magazynu. Tożsamość obszaru roboczego ma taką samą nazwę jak obszar roboczy. W tym dokumencie nazwa obszaru roboczego to, że `myworkspace` tożsamość obszaru roboczego to`myworkspaced`
1. Wybierz pozycję **Zapisz**.
    
## <a name="launch-synapse-studio"></a>Uruchom Synapse Studio

Po utworzeniu obszaru roboczego Synapse masz dwa sposoby otwierania Synapse Studio:
* Otwórz obszar roboczy Synapse w [Azure Portal](https://portal.azure.com) i w górnej części sekcji **Przegląd** wybierz pozycję **Uruchom Synapse Studio** .
* Bezpośrednio przejdź do https://web.azuresynapse.net obszaru roboczego i zaloguj się do niego.

## <a name="create-a-sql-pool"></a>Tworzenie puli SQL

1. W programie Synapse Studio w obszarze nawigacji po lewej stronie wybierz pozycję **zarządzaj > pule SQL**

    > [!NOTE] 
    > Wszystkie obszary robocze Synapse są dostarczane ze wstępnie utworzoną pulą o nazwie **SQL na żądanie**.

1. Wybierz pozycję **+ Nowy** i wprowadź następujące ustawienia:

    |Ustawienie | Sugerowana wartość | 
    |---|---|
    |**Nazwa puli SQL**| `SQLDB1`|
    |**Poziom wydajności**|`DW100C`|

1. Wybierz pozycję **Recenzja + Utwórz** , a następnie wybierz pozycję **Utwórz**.
1. Pula SQL będzie gotowa w ciągu kilku minut.

    > [!NOTE]
    > Synapse Pula programu SQL odpowiada na to, co jest używane jako "Azure SQL Data Warehouse"

Pula SQL zużywa zasoby do rozliczenia, o ile jest uruchomiona. W związku z tym można wstrzymać pulę, gdy jest to konieczne, aby obniżyć koszty.

Po utworzeniu puli SQL zostanie ona skojarzona z bazą danych puli SQL o nazwie **SQLDB1**.

## <a name="create-an-apache-spark-pool"></a>Tworzenie puli Apache Spark

1. W programie Synapse Studio po lewej stronie wybierz pozycję **zarządzaj > Apache Spark pule**
1. Wybierz pozycję **+ Nowy** i wprowadź następujące ustawienia:

    |Ustawienie | Sugerowana wartość | 
    |---|---|
    |**Nazwa puli Apache Spark**|`Spark1`
    |**Rozmiar węzła**| `Small`|
    |**Liczba węzłów**| Ustaw wartość minimalną na 3 i wartość maksymalną na 3.|

1. Wybierz pozycję **Recenzja + Utwórz** , a następnie wybierz pozycję **Utwórz**.
1. Pula Apache Spark będzie gotowa w ciągu kilku sekund.

> [!NOTE]
> Oprócz nazwy Pula Apache Spark nie jest taka sama jak Pula SQL. Jest to tylko kilka podstawowych metadanych, które służą do informowania obszaru roboczego Synapse o sposobie współpracy z platformą Spark. 

Ponieważ są to metadane, nie można uruchamiać ani zatrzymywać pul platformy Spark. 

W przypadku działania platformy Spark w Synapse należy określić pulę platformy Spark do użycia. Pula informuje Synapse, ile zasobów platformy Spark ma używać. Płacisz tylko za Thar zasobów. Gdy aktywnie zatrzymasz korzystanie z puli, zasoby przekroczą limit czasu i zostaną przetworzone ponownie.

> [!NOTE]
> Bazy danych Spark są tworzone niezależnie od pul platformy Spark. Obszar roboczy zawsze ma bazę danych Spark o nazwie **default** i można utworzyć dodatkowe bazy danych platformy Spark.

## <a name="the-sql-on-demand-pool"></a>Pula na żądanie SQL

Każdy obszar roboczy jest dostarczany z wbudowaną i nieusuwającą pulą o nazwie **SQL na żądanie**. Pula na żądanie SQL umożliwia korzystanie z programu SQL bez konieczności tworzenia i zarządzania Synapseą pulą SQL. W przeciwieństwie do innych rodzajów pul, rozliczenia dla SQL na żądanie bazują na ilości danych skanowanych w celu uruchomienia zapytania, a nie liczby zasobów używanych do wykonywania zapytania.

* Program SQL on-Demand ma także własne bazy danych SQL na żądanie, które nie są dostępne niezależnie od puli na żądanie SQL.
* Obecnie obszar roboczy zawsze ma dokładnie jedną pulę na żądanie SQL o nazwie **SQL na żądanie**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Ładowanie przykładowych danych z NYC taksówki do bazy danych SQLDB1

1. W programie Synapse Studio, w górnym niebieskim menu, wybierz pozycję **?** .
1. Wybieranie opcji **wprowadzenie > wprowadzenie do centrum**
1. Na karcie oznaczonej **przykładowe dane zapytania**wybierz pulę SQL o nazwie`SQLDB1`
1. Wybierz pozycję **dane zapytania**. Zobaczysz powiadomienie z informacją o "ładowaniu przykładowych danych", które zostanie wyświetlone, a następnie zniknie.
1. Zobaczysz jasnoniebieski pasek powiadomień znajdujący się w górnej części Synapse Studio wskazujący, że dane są ładowane do SQLDB1. Zaczekaj, aż zmieni kolor na zielony, a następnie Odrzuć go.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Eksplorowanie danych NYC taksówki w puli SQL

1. W programie Synapse Studio przejdź do centrum **danych**
1. Przejdź do **SQLDB1 > tabel**. Zobaczysz kilka tabel, które zostały załadowane.
1. Kliknij prawym przyciskiem myszy obiekt **dbo. Tabela podróży** i wybierz **Nowy skrypt SQL > zaznacz pierwsze 100 wierszy**
1. Zostanie utworzony nowy skrypt SQL i zostanie on automatycznie uruchomiony.
1. Zwróć uwagę, że u góry skryptu SQL **połączenie z programem** jest automatycznie ustawiana na pulę SQL o nazwie SQLDB1.
1. Zastąp tekst skryptu SQL tym kodem i uruchom go.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

1. To zapytanie pokazuje, w jaki sposób łączny czas podróży i średnią odległość podróży odnoszą się do liczby pasażerów
1. W oknie wyników skryptu SQL Zmień widok na **Wykres** , aby wyświetlić wizualizację wyników jako wykres liniowy **.**

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>Ładowanie przykładowych danych z NYC taksówki do bazy danych Spark nyctaxi

W tabeli w programie są dostępne dane `SQLDB1` . Teraz ładujemy go do bazy danych Spark o nazwie "nyctaxi".

1. W programie Synapse Studio przejdź do centrum **opracowywania**
1. Wybierz **+** i wybierz **Notes**
1. W górnej części notesu ustaw wartość **Dołącz do** , aby`Spark1`
1. Wybierz pozycję **Dodaj kod** , aby dodać komórkę kodu notesu i wklej następujący tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Przejdź do centrum **danych** , kliknij prawym przyciskiem myszy pozycję **bazy** danych, a następnie wybierz polecenie **Odśwież**.
1. Teraz powinny zostać wyświetlone następujące bazy danych:
    - SQLDB (Pula SQL)
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizowanie danych z NYC taksówki przy użyciu platformy Spark i notesów

1. Wróć do notesu
1. Utwórz nową komórkę kodu, wprowadź tekst poniżej i uruchom komórkę, aby na przykład NYC dane z taksówkami, które zostały załadowane do `nyctaxi` bazy danych Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Uruchom następujący kod, aby wykonać tę samą analizę wcześniej z pulą SQL `SQLDB1` . Ten kod również zapisuje wyniki analizy w tabeli o nazwie `nyctaxi.passengercountstats` i wizualizowa wyniki.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
    """) 
    display(df)
    df.write.saveAsTable("nyctaxi.passengercountstats")
    ```

1. W obszarze wyniki komórki wybierz pozycję **Wykres** , aby wyświetlić wizualizację danych.
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Dostosowywanie danych wizualizacji danych za pomocą platformy Spark i notesów

Za pomocą notesów można kontrolować sposób renderowania wykresów. Poniższy kod przedstawia prosty przykład przy użyciu popularnych bibliotek `matplotlib` i `seaborn` . Renderuje ten sam rodzaj wykresu liniowego, który był widoczny podczas wykonywania zapytań SQL wcześniej.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```
    
## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Ładowanie danych z tabeli platformy Spark do tabeli puli SQL

Wcześniej skopiowano dane z tabeli puli SQL `SQLDB1.dbo.Trip` do tabeli Spark `nyctaxi.trip` . Następnie używając platformy Spark, agregujemy dane do tabeli Spark `nyctaxi.passengercountstats` . Teraz skopiujemy dane z `nyctaxi.passengercountstats` do tabeli puli SQL o nazwie `SQLDB1.dbo.PassengerCountStats` . 

Uruchom komórkę poniżej w Twoim notesie. Spowoduje to skopiowanie zagregowanej tabeli platformy Spark z powrotem do tabeli puli SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analizowanie danych z NYC taksówki w bazach danych platformy Spark przy użyciu programu SQL na żądanie 

Tabele w bazach danych Spark są automatycznie widoczne i Queryable przez SQL na żądanie.

1. W programie Synapse Studio przejdź do centrum **opracowywania** i Utwórz nowy skrypt SQL
1. Ustaw **połączenie** z programem **SQL na żądanie** 
1. Wklej następujący tekst do skryptu i uruchom skrypt.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > Przy pierwszym uruchomieniu zapytania korzystającego z SQL na żądanie potrwa około 10 sekund na żądanie SQL do zbierania zasobów SQL potrzebnych do uruchamiania zapytań. Kolejne zapytania nie wymagają tego czasu i są znacznie szybsze.
  
## <a name="orchestrate-activities-with-pipelines"></a>Organizowanie działań przy użyciu potoków

W usłudze Azure Synapse można organizować wiele różnych zadań. W tej sekcji zobaczysz, jak łatwo jest.

1. W programie Synapse Studio przejdź **do centrum organizowania** .
1. Wybierz pozycję **+** **potok**. Zostanie utworzony nowy potok.
1. Przejdź do centrum opracowywania i Znajdź wcześniej utworzony Notes.
1. Przeciągnij ten Notes do potoku.
1. W potoku wybierz pozycję **Dodaj wyzwalacz > nowy/Edytuj**.
1. W obszarze **Wybierz wyzwalacz** wybierz pozycję **Nowy**, a następnie w obszarze cykl Ustaw wyzwalacz do uruchomienia co 1 godzinę.
1. Wybierz przycisk **OK**.
1. Wybierz pozycję **Opublikuj wszystko** , a potok będzie uruchamiany co godzinę.
1. Jeśli chcesz, aby potok został uruchomiony teraz bez oczekiwania na następną godzinę, wybierz pozycję **Dodaj wyzwalacz > nowe/Edycja**.

## <a name="working-with-data-in-a-storage-account"></a>Praca z danymi na koncie magazynu

Do tej pory omówione scenariusze dotyczyły danych w bazach danych w obszarze roboczym. Teraz pokażemy sposób pracy z plikami na kontach magazynu. W tym scenariuszu użyjemy podstawowego konta magazynu dla obszaru roboczego i kontenera, który został określony podczas tworzenia obszaru roboczego.

* Nazwa konta magazynu:`contosolake`
* Nazwa kontenera na koncie magazynu:`users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Tworzenie plików CSV i Parquet na koncie magazynu

Uruchom następujący kod w notesie. Tworzy plik CSV i plik Parquet na koncie magazynu

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analizowanie danych na koncie magazynu

1. W programie Synapse Studio przejdź do centrum **danych**
1. Wybierz pozycję **połączone**
1. Przejdź do **konta magazynu > contosolake (podstawowa-niestandardowa)**
1. Wybierz **użytkowników (podstawowy) "**
1. Powinien pojawić się folder o nazwie "NYCTaxi". Wewnątrz powinny być widoczne dwa foldery "PassengerCountStats. csv" i "PassengerCountStats. Parquet".
1. Przejdź do folderu "PassengerCountStats. Parquet".
1. Kliknij prawym przyciskiem myszy plik Parquet wewnątrz, a następnie wybierz pozycję **Nowy Notes**, spowoduje utworzenie notesu z komórką w następujący sposób:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Uruchom komórkę.
1. Kliknij prawym przyciskiem myszy plik Parquet wewnątrz i wybierz pozycję **Nowy skrypt sql > wybierz pozycję pierwsze 100 wierszy**. spowoduje to utworzenie skryptu SQL w następujący sposób:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. W skrypcie w polu **Dołącz do** zostanie ustawiona wartość **SQL na żądanie**.
1. Uruchom skrypt.

## <a name="visualize-data-with-power-bi"></a>Wizualizacja danych przy użyciu usługi Power BI

Na podstawie danych z NYX taksówkami zostały utworzone zagregowane zestawy datadataset w dwóch tabelach:
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

Obszar roboczy Power BI można połączyć z obszarem roboczym Synapse. Dzięki temu można łatwo uzyskiwać dane do obszaru roboczego Power BI, a raporty Power BI można edytować bezpośrednio w obszarze roboczym Synapse.

### <a name="create-a-power-bi-workspace"></a>Tworzenie obszaru roboczego Power BI

1. Zaloguj się do [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Utwórz nowy obszar roboczy Power BI o nazwie `NYCTaxiWorkspace1` .

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Połącz obszar roboczy Synapse z nowym obszarem roboczym Power BI

1. W programie Synapse Studio przejdź do okna **Zarządzanie połączonymi usługami >**.
1. Wybierz pozycję **+ Nowy** i wybierz pozycję **Połącz z Power BI** i ustaw następujące pola:

    |Ustawienie | Sugerowana wartość | 
    |---|---|
    |**Nazwa**|`NYCTaxiWorkspace1`|
    |**Nazwa obszaru roboczego**|`NYCTaxiWorkspace1`|
        
1. Wybierz przycisk **Utwórz**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Tworzenie Power BI zestawu danych, który używa danych w obszarze roboczym Synapse

1. W programie Synapse Studio przejdź do **Power BI tworzenia >**.
1. Przejdź do **NYCTaxiWorkspace1 > Power BI zestawy** danych i wybierz pozycję **Nowy Power BI DataSet**.
1. Umieść kursor nad `SQLDB1` bazą danych i wybierz pozycję **Pobierz plik pbids**.
1. Otwórz pobrany `.pbids` plik. 
1. Spowoduje to uruchomienie Power BI pulpicie i automatyczne połączenie go z usługą `SQLDB1` w obszarze roboczym Synapse.
1. Jeśli zostanie wyświetlone okno dialogowe o nazwie **baza danych programu SQL Server**: a. Wybierz **konto Microsoft**. 
    b. Wybierz pozycję **Zaloguj** się i zaloguj się.
    c. Wybierz pozycję **Połącz**.
1. Zostanie otwarte okno dialogowe **Nawigator** . Gdy tak jest, sprawdź tabelę **PassengerCountStats** i wybierz pozycję **Load (ładowanie**).
1. Zostanie wyświetlone okno dialogowe **Ustawienia połączenia** . Wybierz **zapytanie** bezpośrednie i wybierz **przycisk OK**
1. Wybierz przycisk **raportu** po lewej stronie.
1. Dodaj **Wykres liniowy** do raportu.
    a. Przeciągnij kolumnę **PasssengerCount** do **wizualizacji > osi** b. Przeciągnij kolumny **SumTripDistance** i **AvgTripDistance** do **wizualizacji > wartości**.
1. Na karcie **Narzędzia główne** wybierz pozycję **Publikuj**.
1. Zostanie wyświetlony zapytanie, czy chcesz zapisać zmiany. Wybierz pozycję **Zapisz**.
1. Zostanie wyświetlony zapytanie o wybranie nazwy pliku. Wybierz `PassengerAnalysis.pbix` i wybierz pozycję **Zapisz**.
1. Zostanie wyświetlony zapytanie o **wybranie miejsca docelowego** i wybranie opcji `NYCTaxiWorkspace1` **Wybierz**.
1. Zaczekaj na zakończenie publikowania.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurowanie uwierzytelniania dla zestawu danych

1. Otwórz [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) i **Zaloguj się**
1. Po lewej stronie w obszarze **obszary robocze** wybierz `NYCTaxiWorkspace1` obszar roboczy.
1. Wewnątrz tego obszaru roboczego powinien zostać wyświetlony zestaw danych o nazwie `Passenger Analysis` i Raport `Passenger Analysis` .
1. Umieść kursor nad `PassengerAnalysis` zestawem danych i wybierz ikonę z trzema kropkami, a następnie wybierz pozycję **Ustawienia**.
1. W polu **poświadczenia źródła danych**Ustaw **metodę uwierzytelniania** na **OAuth2** i wybierz pozycję **Zaloguj**.

### <a name="edit-a-report-in-synapse-studio"></a>Edytowanie raportu w programie Synapse Studio

1. Wróć do Synapse Studio i wybierz pozycję **Zamknij i Odśwież** 
1. Przejdź do centrum **opracowywania** 
1. Umieść kursor nad **Power BI** i kliknij węzeł Odśwież **Power BI raporty** .
1. Teraz pod **Power BI** powinna zostać wyświetlona wartość: a. * W obszarze **NYCTaxiWorkspace1 > Power BI zestawy**danych, nowy element dataset o nazwie **PassengerAnalysis**.
    b. * W obszarze **NYCTaxiWorkspace1 > Power BI raporty**nowy raport o nazwie **PassengerAnalysis**.
1. Wybierz raport **PassengerAnalysis** . 
1. Raport zostanie otwarty, a teraz można edytować raport bezpośrednio w programie Synapse Studio.

## <a name="monitor-activities"></a>Monitorowanie działań

1. W programie Synapse Studio przejdź do centrum monitora.
1. W tej lokalizacji można zobaczyć historię wszystkich działań podejmowanych w obszarze roboczym, które są teraz aktywne.
1. Eksploruj **przebiegi potokowe**, **Apache Spark aplikacje**i **żądania SQL** i możesz zobaczyć, co już zostało zrobione w obszarze roboczym.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Azure Synapse Analytics (wersja zapoznawcza)](overview-what-is.md)

