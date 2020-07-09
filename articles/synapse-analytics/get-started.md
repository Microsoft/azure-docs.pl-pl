---
title: 'Samouczek: Rozpoczynanie pracy z usługą Azure Synapse Analytics'
description: W tym samouczku przedstawiono podstawowe kroki konfigurowania usługi Azure Synapse Analytics i korzystania z niej.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791864"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Rozpoczynanie pracy z usługą Azure Synapse Analytics

Ten samouczek przeprowadzi Cię przez podstawowe kroki konfigurowania usługi Azure Synapse Analytics i korzystania z niej.

## <a name="prepare-a-storage-account"></a>Przygotowywanie konta magazynu

1. Otwórz [witrynę Azure Portal](https://portal.azure.com).
1. Utwórz nowe konto magazynu o następujących ustawieniach:

    |Tab|Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|---|
    |Podstawy|**Nazwa konta magazynu**| Możesz nadać mu dowolną nazwę.|W tym dokumencie odwołujemy się do niego jako **contosolake**.|
    |Podstawy|**Rodzaj konta**|Musi być ustawiona na **StorageV2**.||
    |Podstawy|**Lokalizacja**|Wybierz dowolną lokalizację.| Zalecamy, aby obszar roboczy usługi Azure Synapse Analytics i konto Azure Data Lake Storage Gen2 znajdować się w tym samym regionie.|
    |Zaawansowane|**Data Lake Storage Gen2**|**Włączone**| Usługa Azure Synapse działa tylko z kontami magazynu, na których to ustawienie jest włączone.|
    |||||

1. Po utworzeniu konta magazynu wybierz pozycję **Kontrola dostępu (IAM)** w okienku po lewej stronie. Następnie przypisz następujące role lub upewnij się, że są już przypisane:
    1. Przypisz siebie do roli **właściciela** .
    1. Przypisz siebie do roli **właściciela danych obiektu blob magazynu** .
1. W okienku po lewej stronie wybierz pozycję **kontenery** i Utwórz kontener.
1. Można nadać kontenerowi dowolną nazwę. W tym dokumencie zmienimy nazwy **użytkowników**kontenera.
1. Zaakceptuj domyślne ustawienie **poziomu dostępu publicznego**, a następnie wybierz pozycję **Utwórz**.

W poniższym kroku skonfigurujesz obszar roboczy usługi Azure Synapse, aby używał tego konta magazynu jako konta magazynu "podstawowe" i kontenera do przechowywania danych obszaru roboczego. Obszar roboczy przechowuje dane w tabelach Apache Spark. Przechowuje dzienniki aplikacji platformy Spark w folderze o nazwie **/Synapse/WorkspaceName**.

## <a name="create-a-synapse-workspace"></a>Tworzenie obszaru roboczego Synapse

1. Otwórz [Azure Portal](https://portal.azure.com)i w górnej części Szukaj **Synapse**.
1. W wynikach wyszukiwania w obszarze **usługi**wybierz pozycję **Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)**.
1. Wybierz pozycję **Dodaj** , aby utworzyć obszar roboczy przy użyciu tych ustawień:

    |Tab|Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|---|
    |Podstawy|**Nazwa obszaru roboczego**|Możesz wywoływać wszystko.| W tym dokumencie będziemy używać funkcji My **Workspace**.|
    |Podstawy|**Region**|Dopasuj region konta magazynu.|

1. W obszarze **wybierz Data Lake Storage Gen 2**wybierz wcześniej utworzone konto i kontener.
1. Wybierz pozycję **Recenzja + Utwórz**  >  **Utwórz**. Obszar roboczy jest gotowy w ciągu kilku minut.

## <a name="verify-access-to-the-storage-account"></a>Weryfikowanie dostępu do konta magazynu

Zarządzane tożsamości dla obszaru roboczego usługi Azure Synapse prawdopodobnie mają już dostęp do konta magazynu. Wykonaj następujące kroki, aby upewnić się, że:

1. Otwórz [Azure Portal](https://portal.azure.com) i konto magazynu podstawowego wybrane dla Twojego obszaru roboczego.
1. Wybierz pozycję **Kontrola dostępu (IAM)** w okienku po lewej stronie.
1. Przypisz poniższe role lub upewnij się, że są już przypisane. Używamy tej samej nazwy dla tożsamości obszaru roboczego i nazwy obszaru roboczego.
    1. W przypadku roli **współautor danych obiektów blob magazynu** na koncie magazynu należy przypisać obszar **roboczy** jako tożsamość obszaru roboczego.
    1. Przypisz **jako nazwę obszaru roboczego.**

1. Wybierz pozycję **Zapisz**.

## <a name="open-synapse-studio"></a>Otwórz Synapse Studio

Po utworzeniu obszaru roboczego usługi Azure Synapse dostępne są dwa sposoby otwierania programu Synapse Studio:

* Otwórz obszar roboczy Synapse w [Azure Portal](https://portal.azure.com). W górnej części sekcji **Przegląd** wybierz pozycję Uruchom program **Synapse Studio**.
* Przejdź do https://web.azuresynapse.net obszaru roboczego i zaloguj się do niego.

## <a name="create-a-sql-pool"></a>Tworzenie puli SQL

1. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami SQL**.
1. Wybierz pozycję **nowe** i wprowadź następujące ustawienia:

    |Ustawienie | Sugerowana wartość | 
    |---|---|---|
    |**Nazwa puli SQL**| **SQLDB1**|
    |**Poziom wydajności**|**DW100C**|
    |||

1. Wybierz pozycję **Recenzja + Utwórz**  >  **Utwórz**. Pula SQL będzie gotowa w ciągu kilku minut. Pula SQL jest skojarzona z bazą danych puli SQL o nazwie **SQLDB1**.

Pula SQL zużywa zasoby do rozliczenia, o ile jest ona aktywna. Pulę można wstrzymać później, aby zmniejszyć koszty.

## <a name="create-an-apache-spark-pool"></a>Tworzenie puli Apache Spark

1. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami Apache Spark**.
1. Wybierz pozycję **nowe** i wprowadź następujące ustawienia:

    |Ustawienie | Sugerowana wartość | 
    |---|---|---|
    |**Nazwa puli Apache Spark**|**Spark1**
    |**Rozmiar węzła**| **Mały**|
    |**Liczba węzłów**| Ustaw wartość minimalną na 3 i wartość maksymalną na 3.|

1. Wybierz pozycję **Recenzja + Utwórz**  >  **Utwórz**. Pula Apache Spark będzie gotowa w ciągu kilku sekund.

> [!NOTE]
> Oprócz nazwy Pula Apache Spark nie jest taka sama jak Pula SQL. Jest to tylko kilka podstawowych metadanych, których można użyć do poinformowania obszaru roboczego usługi Azure Synapse, jak korzystać z platformy Spark.

Ponieważ są to metadane, nie można uruchomić ani zatrzymać pul platformy Spark.

Podczas wykonywania działania platformy Spark w usłudze Azure Synapse należy określić pulę platformy Spark do użycia. Pula informuje platformę Azure Synapse, ile zasobów platformy Spark ma używać. Płacisz tylko za wykorzystane zasoby. Gdy aktywne zaprzestanie korzystania z puli, zasoby są automatycznie przekroczenia limitu czasu i są odtwarzane.

> [!NOTE]
> Bazy danych Spark są tworzone niezależnie od pul platformy Spark. Obszar roboczy zawsze ma bazę danych Spark o nazwie **default**. Można utworzyć dodatkowe bazy danych platformy Spark.

## <a name="the-sql-on-demand-pool"></a>Pula na żądanie SQL

Każdy obszar roboczy jest dostarczany ze wstępnie utworzoną pulą o nazwie **SQL na żądanie**. Nie można usunąć tej puli. Pula na żądanie SQL umożliwia korzystanie z programu SQL bez konieczności tworzenia lub oceniania zarządzania pulą SQL w usłudze Azure Synapse.

W przeciwieństwie do innych rodzajów pul, rozliczenia dla SQL na żądanie bazują na ilości danych skanowanych w celu uruchomienia zapytania, a nie liczby zasobów używanych do wykonywania zapytania.

* SQL na żądanie ma własne bazy danych SQL na żądanie, które nie są dostępne niezależnie od puli na żądanie SQL.
* Obszar roboczy zawsze ma dokładnie jedną pulę na żądanie SQL o nazwie **SQL na żądanie**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Ładowanie przykładowych danych z NYC taksówki do bazy danych SQLDB1

1. W programie Synapse Studio, w górnym niebieskim menu, wybierz pozycję **?** .
1. Wybierz pozycję **wprowadzenie**— wprowadzenie do  >  **centrum**.
1. Na karcie oznaczone **przykładowe dane zapytania**wybierz pulę SQL o nazwie **SQLDB1**.
1. Wybierz pozycję **dane zapytania**. Zostanie wyświetlone powiadomienie "Ładowanie przykładowych danych". Jasnoniebieski pasek stanu w górnej części programu Synapse Studio wskazuje, że dane są ładowane do SQLDB1.
1. Gdy pasek stanu zmieni kolor na zielony, Odrzuć go.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Eksplorowanie danych NYC taksówki w puli SQL

1. W programie Synapse Studio przejdź do centrum **danych** .
1. Przejdź do **SQLDB1**  >  **tabel**SQLDB1. Zobaczysz kilka załadowanych tabel.
1. Kliknij prawym przyciskiem myszy obiekt **dbo. Tabelę podróży** i wybierz pozycję **Nowy skrypt SQL**  >  **Wybierz pierwsze 100 wierszy**.
1. Poczekaj, aż zostanie utworzony i uruchomiony nowy skrypt SQL.
1. Zwróć uwagę, że u góry skryptu SQL **połączenie z programem** jest automatycznie ustawiana na pulę SQL o nazwie **SQLDB1**.
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

    To zapytanie pokazuje, w jaki sposób łączny czas podróży i średnią odległość podróży odnoszą się do liczby osób.
1. W oknie wyników skryptu SQL Zmień **Widok** na **Wykres** , aby wyświetlić wizualizację wyników jako wykres liniowy.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Ładowanie danych z NYC taksówki do bazy danych Spark nyctaxi

Dostępne są dane w tabeli w **SQLDB1**. Załaduj je do bazy danych Spark o nazwie **nyctaxi**.

1. W programie Synapse Studio przejdź do centrum **opracowywania** .
1. Wybierz pozycję **+**  >  **Notes**.
1. W górnej części notesu ustaw wartość **Dołącz do** na **Spark1**.
1. Wybierz pozycję **Dodaj kod** , aby dodać komórkę kodu notesu, a następnie wklej następujący tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Przejdź do centrum **danych** , kliknij prawym przyciskiem myszy pozycję **bazy danych**, a następnie wybierz polecenie **Odśwież**. Powinny zostać wyświetlone następujące bazy danych:

    - **SQLDB1** (Pula SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizowanie danych z NYC taksówki przy użyciu platformy Spark i notesów

1. Wróć do notesu.
1. Utwórz nową komórkę kodu i wprowadź następujący tekst. Następnie uruchom komórkę, aby wyświetlić dane z NYC taksówki załadowane do bazy danych **nyctaxi** Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Uruchom następujący kod, aby wykonać tę samą analizę, która była wcześniej z pulą SQL **SQLDB1**. Ten kod zapisuje wyniki analizy w tabeli o nazwie **nyctaxi. passengercountstats** i wizualizowa wyniki.

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

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Dostosowywanie wizualizacji danych za pomocą platformy Spark i notesów

Możesz kontrolować sposób renderowania wykresów za pomocą notesów. Poniższy kod przedstawia prosty przykład. Używa popularnych bibliotek **matplotlib** i **Seaborn**. Kod renderuje ten sam rodzaj wykresu liniowego jako zapytania SQL, które zostały wcześniej uruchomione.

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

Wcześniej skopiowano dane z tabeli puli SQL **SQLDB1. dbo. Trip** do tabeli Spark **nyctaxi. Trip**. Następnie używając platformy Spark, agregujemy dane do tabeli Spark **nyctaxi. passengercountstats**. Teraz skopiujemy dane z **nyctaxi. passengercountstats** do tabeli puli SQL o nazwie **SQLDB1. dbo. passengercountstats**.

Uruchom następującą komórkę w notesie. Kopiuje tabelę zagregowanych danych platformy Spark z powrotem do tabeli puli SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analizowanie danych z NYC taksówki w bazach danych platformy Spark przy użyciu programu SQL na żądanie

Tabele w bazach danych platformy Spark są automatycznie widoczne i mogą być wysyłane do nich zapytania przez SQL na żądanie.

1. W programie Synapse Studio przejdź do centrum **tworzenia** i Utwórz nowy skrypt SQL.
1. Ustaw wartość opcji **Połącz** z na **żądanie na SQL**.
1. Wklej następujący tekst do skryptu i uruchom skrypt.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Przy pierwszym uruchomieniu zapytania korzystającego z SQL na żądanie trwa około 10 sekund dla SQL na żądanie w celu zebrania zasobów SQL potrzebnych do uruchamiania zapytań. Kolejne zapytania będą znacznie szybsze.
  
## <a name="orchestrate-activities-with-pipelines"></a>Organizowanie działań przy użyciu potoków

W usłudze Azure Synapse można organizować wiele różnych zadań.

1. W programie Synapse Studio przejdź **do centrum organizowania** .
1. Wybierz pozycję **+**  >  **potok** , aby utworzyć nowy potok.
1. Przejdź do centrum **opracowywania** i Znajdź wcześniej utworzony Notes.
1. Przeciągnij ten Notes do potoku.
1. W potoku wybierz pozycję **Dodaj wyzwalacz**  >  **Nowy/Edytuj**.
1. W obszarze **Wybierz wyzwalacz**wybierz pozycję **Nowy**, a następnie w obszarze **cykl** Ustaw wyzwalacz do uruchomienia co 1 godzinę.
1. Wybierz przycisk **OK**.
1. Wybierz pozycję **Opublikuj wszystkie**. Potok jest uruchamiany co godzinę.
1. Aby uruchomić potok teraz, bez czekania na następną godzinę, wybierz pozycję **Dodaj wyzwalacz**  >  **Nowy/Edytuj**.

## <a name="work-with-data-in-a-storage-account"></a>Pracuj z danymi na koncie magazynu

Do tej pory omówione zostały scenariusze, w których dane znajdują się w bazach danych w obszarze roboczym. Teraz pokażemy, jak korzystać z plików na kontach magazynu. W tym scenariuszu użyjemy podstawowego konta magazynu obszaru roboczego i kontenera, który został określony podczas tworzenia obszaru roboczego.

* Nazwa konta magazynu: **contosolake**
* Nazwa kontenera na koncie magazynu: **Użytkownicy**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Tworzenie plików CSV i Parquet na koncie magazynu

Uruchom następujący kod w notesie. Tworzy plik CSV i plik Parquet na koncie magazynu.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analizowanie danych na koncie magazynu

1. W programie Synapse Studio przejdź do centrum **danych** , a następnie wybierz pozycję **połączone**.
1. Przejdź do pozycji **konta magazynu**mój  >  **obszar roboczy (podstawowe-contosolake)**.
1. Wybierz pozycję **Użytkownicy (podstawowy)**. Powinien zostać wyświetlony folder **NYCTaxi** . Wewnątrz powinny być widoczne dwa foldery o nazwie **PassengerCountStats.csv** i **PassengerCountStats. Parquet**.
1. Otwórz folder **PassengerCountStats. Parquet** . Wewnątrz zostanie wyświetlony plik Parquet o nazwie, takiej jak *część-00000-2638e00c-0790-496b-A523-578da9a15019-C000. przyciąganie. Parquet*.
1. Kliknij prawym przyciskiem myszy pozycję **Parquet**, a następnie wybierz pozycję **Nowy Notes**. Tworzy Notes o takiej samej komórce:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Uruchom komórkę.
1. Kliknij prawym przyciskiem myszy plik Parquet wewnątrz, a następnie wybierz pozycję **Nowy skrypt SQL**  >  **zaznacz pierwsze 100 wierszy**. Tworzy skrypt SQL podobny do tego:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     W skrypcie pole **Dołącz do** ma ustawioną wartość **SQL na żądanie**.

1. Uruchom skrypt.

## <a name="visualize-data-with-power-bi"></a>Wizualizacja danych przy użyciu usługi Power BI

Na podstawie danych z NYC taksówkami zostały utworzone zagregowane zestawy datadataset w dwóch tabelach:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Obszar roboczy Power BI można połączyć z obszarem roboczym usługi Azure Synapse. Dzięki temu można łatwo uzyskać dane do obszaru roboczego Power BI. Raporty dotyczące Power BI można edytować bezpośrednio w obszarze roboczym usługi Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Tworzenie obszaru roboczego Power BI

1. Zaloguj się do [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Utwórz nowy obszar roboczy Power BI o nazwie **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Połącz obszar roboczy usługi Azure Synapse z nowym obszarem roboczym Power BI

1. W programie Synapse Studio przejdź do pozycji **Zarządzaj**  >  **połączonymi usługami**.
1. Wybierz pozycję **Nowy**  >  **Połącz z Power BI**, a następnie ustaw następujące pola:

    |Ustawienie | Sugerowana wartość | 
    |---|---|
    |**Nazwa**|**NYCTaxiWorkspace1**|
    |**Nazwa obszaru roboczego**|**NYCTaxiWorkspace1**|

1. Wybierz przycisk **Utwórz**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Tworzenie Power BI zestawu danych, który używa danych w obszarze roboczym usługi Azure Synapse

1. W programie Synapse Studio przejdź do pozycji **opracowywanie**  >  **Power BI**.
1. Przejdź do **NYCTaxiWorkspace1**  >  **Power BI zestawy** danych i wybierz pozycję **Nowy Power BI zestaw danych**.
1. Umieść kursor nad bazą danych **SQLDB1** i wybierz pozycję **Pobierz plik pbids**.
1. Otwórz pobrany plik **pbids** . Zostanie otwarty Power BI pulpitu i automatycznie nawiąże połączenie z usługą **SQLDB1** w obszarze roboczym usługi Azure Synapse.
1. Jeśli zostanie wyświetlone okno dialogowe o nazwie **baza danych programu SQL Server**:
    1. Wybierz **konto Microsoft**.
    1. Wybierz pozycję **Zaloguj** się i zaloguj się na swoim koncie.
    1. Wybierz pozycję **Połącz**.
1. Po otwarciu okna dialogowego **nawigatora** sprawdź tabelę **PassengerCountStats** , a następnie wybierz pozycję **Load (ładowanie**).
1. Po wyświetleniu okna dialogowego **Ustawienia połączenia** wybierz pozycję **zapytanie**bezpośrednie  >  **OK**.
1. Wybierz przycisk **raportu** po lewej stronie.
1. Dodaj **Wykres liniowy** do raportu.
    1. Przeciągnij kolumnę **PassengerCount** do osi **wizualizacje**  >  **Axis**.
    1. Przeciągnij kolumny **SumTripDistance** i **AvgTripDistance** na wartości **wizualizacje**  >  **Values**.
1. Na karcie **Narzędzia główne** wybierz pozycję **Publikuj**.
1. Wybierz przycisk **Zapisz**, aby zapisać zmiany.
1. Wybierz nazwę pliku **PassengerAnalysis. pbix**, a następnie wybierz pozycję **Zapisz**.
1. W obszarze **Wybierz lokalizację docelową**wybierz pozycję **NYCTaxiWorkspace1**, a następnie kliknij pozycję **Wybierz**.
1. Zaczekaj na zakończenie publikowania.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurowanie uwierzytelniania dla zestawu danych

1. Otwórz [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) i **Zaloguj się**.
1. Po lewej stronie, w obszarze **obszary robocze**, wybierz obszar roboczy **NYCTaxiWorkspace1** .
1. W tym obszarze roboczym Znajdź zestaw danych o nazwie **Analiza pasażera** i raport o nazwie **Analiza pasażera**.
1. Zatrzymaj wskaźnik myszy nad zestawem danych **PassengerAnalysis** , wybierz przycisk wielokropka (...), a następnie wybierz pozycję **Ustawienia**.
1. W polu **poświadczenia źródła danych**Ustaw **metodę uwierzytelniania** na **OAuth2**, a następnie wybierz pozycję **Zaloguj**.

### <a name="edit-a-report-in-synapse-studio"></a>Edytowanie raportu w programie Synapse Studio

1. Wróć do Synapse Studio i wybierz pozycję **Zamknij i Odśwież**.
1. Przejdź do centrum **opracowywania** .
1. Umieść kursor nad **Power BI** , a następnie wybierz węzeł Odśwież **Power BI raporty** .
1. W obszarze **Power BI** powinna zostać wyświetlona:
    1. W obszarze **NYCTaxiWorkspace1**  >  **Power BI zestawy**danych nowy element dataset o nazwie **PassengerAnalysis**.
    1. W obszarze **NYCTaxiWorkspace1**  >  **Power BI raporty**nowy raport o nazwie **PassengerAnalysis**.
1. Wybierz raport **PassengerAnalysis** . Raport zostanie otwarty i będzie można go edytować bezpośrednio w programie Synapse Studio.

## <a name="monitor-activities"></a>Monitorowanie działań

1. W programie Synapse Studio przejdź do centrum **monitora** .
1. W tej lokalizacji można zobaczyć historię wszystkich działań podejmowanych w obszarze roboczym, które są teraz aktywne.
1. Eksploruj **przebiegi potokowe**, **Apache Spark aplikacje**i **żądania SQL** , aby zobaczyć, co zostało już zrobione w obszarze roboczym.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)](overview-what-is.md).

