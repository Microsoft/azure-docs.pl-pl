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
ms.openlocfilehash: 29e5ba149a99f350b8ad9244605470d8b9b61597
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749500"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Wprowadzenie z usługą Azure Synapse Analytics

Ten samouczek przeprowadzi Cię przez wszystkie podstawowe kroki niezbędne do skonfigurowania usługi Azure Synapse Analytics i korzystania z niej.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Przygotowywanie konta magazynu do użycia z obszarem roboczym Synapse

* Otwórz [Azure Portal](https://portal.azure.com)
* Utwórz nowe konto magazynu z następującymi ustawieniami:
    * Na karcie **podstawy**

    |Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    |**Nazwa konta magazynu**| Możesz nadać mu dowolną nazwę.|W tym dokumencie odwołujemy się do niego jako `contosolake` .
    |**Rodzaj konta**|Musi być ustawiona na`StorageV2`||
    |**Lokalizacja**|Możesz wybrać dowolną lokalizację| Zalecamy, aby obszar roboczy Synapse i konto Azure Data Lake Storage (ADLS) Gen2 są w tym samym regionie.|
    ||||

    * Na karcie **Zaawansowane**

    |Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    |**Usługa Data Lake Storage 2. generacji**|`Enabled`| Usługa Azure Synapse działa tylko z kontami magazynu, na których to ustawienie jest włączone.|
    ||||

* Po utworzeniu konta magazynu ustaw te przypisania ról lub upewnij się, że zostały już przypisane. Na koncie magazynu wybierz pozycję **Kontrola dostępu (IAM)** z lewego okienka nawigacji.
    * Przypisz siebie do roli **właściciela** na koncie magazynu
    * Przypisz siebie do roli **właściciela danych obiektów blob magazynu** na koncie magazynu
* W okienku nawigacji po lewej stronie wybierz pozycję **kontenery** i Utwórz kontener. Możesz nadać mu dowolną nazwę. Zaakceptuj domyślny **poziom dostępu publicznego**. W tym dokumencie wywołamy kontener `users` . Wybierz przycisk **Utwórz**. 

## <a name="create-a-synapse-workspace"></a>Tworzenie obszaru roboczego Synapse

* Otwórz [Azure Portal](https://portal.azure.com) i Znajdź na początku `Synapse` .
* W wynikach wyszukiwania w obszarze **usługi**wybierz pozycję **Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)**
* Wybierz pozycję **+ Dodaj**
* Karta **podstawy** :

    |Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    |**Nazwa obszaru roboczego**|Możesz wywoływać wszystko.| W tym dokumencie będziemy używać`myworkspace`
    |**Region**|Dopasuj region konta magazynu||
    |||

* W obszarze **wybierz Data Lake Storage Gen 2** wybierz wcześniej utworzone konto i kontener

> [!NOTE]
> Konto magazynu wybrane w tym miejscu będzie określane jako "podstawowe" konto magazynu obszaru roboczego Synapse

* Wybierz pozycję **Przegląd + utwórz**. Wybierz przycisk **Utwórz**. Obszar roboczy będzie gotowy w ciągu kilku minut.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Sprawdź, czy plik MSI obszaru roboczego Synapse ma dostęp do konta magazynu

Mogło to być już zrobione. W każdym przypadku należy zweryfikować.

* Otwórz [Azure Portal](https://portal.azure.com) Otwórz konto magazynu podstawowego wybrane dla Twojego obszaru roboczego.
* Upewnij się, że następujące przypisanie istnieje lub utwórz je, jeśli nie
    * Rola współautor danych obiektów blob magazynu na koncie magazynu w obszarze roboczym.
    * Aby przypisać tę rolę do obszaru roboczego, wybierz rolę współautor danych obiektów blob magazynu, pozostaw domyślny **dostęp do** i w polu **Wybierz** wpisz nazwę obszaru roboczego. Wybierz pozycję **Zapisz**.
    
## <a name="launch-synapse-studio"></a>Uruchom Synapse Studio

Po utworzeniu obszaru roboczego Synapse masz dwa sposoby otwierania Synapse Studio:
* Otwórz obszar roboczy Synapse w [Azure Portal](https://portal.azure.com) i w górnej części sekcji **Przegląd** wybierz pozycję **Uruchom Synapse Studio** .
* Bezpośrednio przejdź do https://web.azuresynapse.net obszaru roboczego i zaloguj się do niego.

## <a name="create-a-sql-pool"></a>Tworzenie puli SQL

* W programie Synapse Studio po lewej stronie przejdź do strony **zarządzaj > pule SQL**
* Uwaga: wszystkie obszary robocze Synapse są dostarczane ze wstępnie utworzoną pulą o nazwie **SQL na żądanie**.
* Wybierz pozycję **+ Nowy** i wprowadź następujące ustawienia:

    |Ustawienie | Sugerowana wartość | 
    |---|---|---|
    |**Nazwa puli SQL**| `SQLDB1`|
    |**Poziom wydajności**|`DW100C`|
* Wybierz pozycję **Recenzja + Utwórz** , a następnie wybierz pozycję **Utwórz**.
* Pula będzie gotowa w ciągu kilku minut.

> [!NOTE]
> Synapse Pula programu SQL odpowiada na to, co jest używane jako "Azure SQL Data Warehouse"

* Pula SQL zużywa zasoby do rozliczenia, o ile jest uruchomiona. W związku z tym można wstrzymać pulę, gdy jest to konieczne, aby obniżyć koszty.
* Po utworzeniu puli SQL zostanie ona skojarzona z bazą danych puli SQL o nazwie **SQLDB1**.

## <a name="create-an-apache-spark-pool-for-azure-synapse-analytics"></a>Tworzenie puli Apache Spark dla usługi Azure Synapse Analytics

* W programie Synapse Studio po lewej stronie wybierz pozycję **zarządzaj > Apache Spark pule**
* Wybierz pozycję **+ Nowy** i wprowadź następujące ustawienia:

    |Ustawienie | Sugerowana wartość | 
    |---|---|---|
    |**Nazwa puli Apache Spark**|`Spark1`
    |**Rozmiar węzła**| `Small`|
    |**Liczba węzłów**| Ustaw wartość minimalną na 3 i wartość maksymalną na 3.|
    |||

* Wybierz pozycję **Recenzja + Utwórz** , a następnie wybierz pozycję **Utwórz**.
* Pula Apache Spark będzie gotowa w ciągu kilku sekund.

> [!NOTE]
> Oprócz nazwy Pula Apache Spark nie jest taka sama jak Pula SQL. Jest to tylko kilka podstawowych metadanych, które służą do informowania obszaru roboczego Synapse o sposobie współpracy z platformą Spark. 

* Ponieważ nie można uruchamiać ani zatrzymywać pul platformy Spark metadanych. 
* W przypadku działania platformy Spark w Synapse należy określić pulę platformy Spark do użycia. Pula informuje Synapse, ile zasobów platformy Spark ma używać. Płacisz tylko za Thar zasobów. Gdy aktywnie zatrzymasz korzystanie z puli, zasoby zostaną automatycznie przekroczą limit czasu i zostaną ponownie wykonane.
> [!NOTE]
> Bazy danych Spark są tworzone niezależnie od pul platformy Spark. Obszar roboczy zawsze ma bazę danych Spark o nazwie **default** i można utworzyć dodatkowe bazy danych platformy Spark.

## <a name="sql-on-demand-pools"></a>Pule na żądanie SQL

SQL na żądanie jest specjalnym rodzajem puli SQL, która jest zawsze dostępna w obszarze roboczym Synapse. Umożliwia ona korzystanie z programu SQL bez konieczności tworzenia i zarządzania Synapseą pulą SQL.

> [!NOTE]
> W przeciwieństwie do innych rodzajów pul, rozliczenia dla SQL na żądanie bazują na ilości danych skanowanych w celu uruchomienia zapytania, a nie liczby zasobów używanych do wykonywania zapytania.

* SQL na żądanie ma również własny rodzaj baz danych SQL na żądanie, które istnieją niezależnie od dowolnej puli SQL na żądanie.
* Obecnie obszar roboczy zawsze ma dokładnie jedną pulę na żądanie SQL o nazwie **SQL na żądanie**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Ładowanie przykładowych danych z NYC taksówki do bazy danych SQLDB1

* W programie Synapse Studio, w górnym niebieskim menu, wybierz pozycję **?** .
* Wybieranie opcji **wprowadzenie > wprowadzenie do centrum**
* Na karcie z etykietą **dane przykładowe zapytanie** wybierz pulę SQL o nazwie`SQLDB1`
* Wybierz pozycję **dane zapytania**. Zobaczysz powiadomienie z informacją "Ładowanie przykładowych danych", które pojawi się, a następnie zniknie.
* Zobaczysz jasnoniebieski pasek powiadomień znajdujący się w górnej części Synapse Studio wskazujący, że dane są ładowane do SQLDB1. Zaczekaj, aż zmieni kolor na zielony, a następnie Odrzuć go.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Eksplorowanie danych NYC taksówki w puli SQL

* W programie Synapse Studio przejdź do centrum **danych**
* Przejdź do **SQLDB1 > tabel**. Zobaczysz kilka tabel, które zostały załadowane.
* Kliknij prawym przyciskiem myszy obiekt **dbo. Tabela podróży** i wybierz **Nowy skrypt SQL > zaznacz pierwsze 100 wierszy**
* Zostanie utworzony nowy skrypt SQL i zostanie on automatycznie uruchomiony.
* Zwróć uwagę, że u góry skryptu SQL **połączenie z programem** jest automatycznie ustawiana na pulę SQL o nazwie SQLDB1.
* Zastąp tekst skryptu SQL tym kodem i uruchom go.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

* To zapytanie pokazuje, w jaki sposób łączny czas podróży i średnią odległość podróży odnoszą się do liczby pasażerów
* W oknie wyników skryptu SQL Zmień widok na **Wykres** , aby wyświetlić wizualizację wyników jako wykres liniowy **.**

## <a name="create-a-spark-database-and-load-the-nyc-taxi-data-into-it"></a>Tworzenie bazy danych Spark i ładowanie do niej danych z NYC

Dane są dostępne w bazie danych puli SQL. Teraz ładujemy go do bazy danych Spark.

* W programie Synapse Studio przejdź do centrum opracowywania * *.
* Wybierz **+** i wybierz **Notes**
* W górnej części notesu ustaw wartość **Dołącz do** , aby`Spark1`
* Wybierz pozycję **Dodaj kod** , aby dodać komórkę kodu notesu i wklej następujący tekst:

    ```scala
    %% spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

 * Przejdź do centrum danych, kliknij prawym przyciskiem myszy pozycję bazy danych, a następnie wybierz polecenie **Odśwież** .
 * Teraz powinny zostać wyświetlone następujące bazy danych:
     * SQLDB (Pula SQL)
     * nyctaxi (Spark)
      
 ## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizowanie danych z NYC taksówki przy użyciu platformy Spark i notesów

 * Wróć do notesu
 * Utwórz nową komórkę kodu, wprowadź tekst poniżej i uruchom komórkę.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

 * Uruchom ten kod, aby wykonać tę samą analizę wcześniej z pulą SQL

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

 * W obszarze wyniki komórki wybierz pozycję **Wykres** , aby wyświetlić wizualizację danych.
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Dostosowywanie danych wizualizacji danych za pomocą platformy Spark i notesów

Za pomocą notesów platformy Spark możesz kontrolować dokładnie sposób renderowania wykresów. Poniższy kod przedstawia prosty przykład przy użyciu popularnych bibliotek matplotlib i morskich. Renderuje ten sam wykres, który był widoczny podczas uruchamiania zapytań SQL.

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

Wcześniej skopiowano dane z bazy danych puli SQL do bazy danych Spark. Przy użyciu platformy Spark dane są agregowane w nyctaxi. passengercountstats. Teraz uruchom poniższą komórkę w notesie i skopiujesz tabelę zagregowaną z powrotem do bazy danych puli SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analizowanie danych z NYC taksówki w bazach danych platformy Spark przy użyciu języka SQL na żądanie 

* Tabele w bazach danych Spark są automatycznie widoczne i Queryable przez SQL na żądanie
* W programie Synapse Studio przejdź do centrum opracowywania i Utwórz nowy skrypt SQL
* Ustaw **połączenie** z programem **SQL na żądanie** 
* Wklej następujący tekst do skryptu:

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

* Wybierz pozycję **Uruchom**
* Uwaga: pierwsze uruchomienie tego elementu zajmie około 10 sekund na żądanie SQL w celu zebrania zasobów SQL potrzebnych do uruchamiania zapytań. Kolejne zapytania nie będą wymagały tego czasu.
  
## <a name="use-pipelines-to-orchestrate-activities"></a>Korzystanie z potoków do organizowania działań

W usłudze Azure Synapse można organizować wiele różnych zadań. W tej sekcji zobaczysz, jak łatwo jest.

* W programie Synapse Studio przejdź do centrum organizowania.
* Wybierz pozycję **+** **potok**. Zostanie utworzony nowy potok.
* Przejdź do centrum opracowywania i Znajdź wcześniej utworzone notesy.
* Przeciągnij ten Notes do potoku.
* W potoku wybierz pozycję **Dodaj wyzwalacz > nowy/Edytuj**.
* W obszarze **Wybierz wyzwalacz** wybierz pozycję **Nowy**, a następnie w obszarze cykl Ustaw wyzwalacz do uruchomienia co 1 godzinę.
* Wybierz przycisk **OK**.
* Wybierz pozycję **Opublikuj wszystko** , a potok będzie uruchamiany co godzinę.
* Jeśli chcesz, aby potok został uruchomiony teraz bez oczekiwania na następną godzinę, wybierz pozycję **Dodaj wyzwalacz > nowe/Edycja**.

## <a name="working-with-data-in-a-storage-account"></a>Praca z danymi na koncie magazynu

Do tej pory zostały omówione scenariusze w bazach danych. Teraz pokażemy, w jaki sposób usługa Azure Synapse może analizować proste pliki na koncie magazynu. W tym scenariuszu użyjemy konta magazynu i kontenera, do którego połączono obszar roboczy.

Nazwa konta magazynu: contosolake nazwę kontenera na koncie magazynu: Użytkownicy

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Tworzenie plików CSV i Parquet na koncie magazynu

Uruchom następujący kod w notesie. Tworzy dane CSV i Parquet na koncie magazynu

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analizowanie danych na koncie magazynu

* W programie Synapse Studio przejdź do centrum **danych**
* Wybierz pozycję **połączone**
* Przejdź do **konta magazynu > WorkspaceName (Primary-contosolake)**
* Wybierz **użytkowników (podstawowy) "**
* Powinien pojawić się folder o nazwie "NYCTaxi". Wewnątrz powinny być widoczne dwa foldery "PassengerCountStats. csv" i "PassengerCountStats. Parquet".
* Przejdź do folderu "PassengerCountStats. Parquet".
* Kliknij prawym przyciskiem myszy plik Parquet wewnątrz, a następnie wybierz pozycję Nowy Notes, spowoduje utworzenie notesu z komórką w następujący sposób:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

* Uruchom komórkę, aby przeanalizować plik Parquet za pomocą platformy Spark.
* Kliknij prawym przyciskiem myszy plik Parquet wewnątrz i wybierz pozycję Nowy **skrypt SQL > wybierz pozycję pierwsze 100 wierszy**. spowoduje to utworzenie notesu z komórką podobną do tego:

    ```py
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
* Skrypt zostanie dołączony do bazy danych **SQL na żądanie** Uruchom skrypt. Zwróć uwagę na to, że program wnioskuje o schemat z pliku Parquet.

## <a name="visualize-data-with-power-bi"></a>Wizualizacja danych przy użyciu usługi Power BI

Dane można teraz łatwo analizować i wizualizować w Power BI. Synapse oferuje unikatową integrację, która umożliwia łączenie obszaru roboczego Power BI z obszarem roboczym Synapse. Przed rozpoczęciem należy najpierw wykonać kroki opisane w tym [przewodniku szybki start](quickstart-power-bi.md) , aby połączyć obszar roboczy Power BI.

### <a name="create-a-power-bi-workspace-and-link-it-to-your-synapse-workspace"></a>Utwórz obszar roboczy Power BI i połącz go z obszarem roboczym Synapse

* Zaloguj się do [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
* Utwórz nowy obszar roboczy Power BI o nazwie `NYCTaxiWorkspace1` .
* W programie Synapse Studio przejdź do okna **Zarządzanie połączonymi usługami >**.
* Wybierz pozycję **+ Nowy** i wybierz pozycję **Połącz z Power BI** i ustaw następujące pola:

    |Ustawienie | Sugerowana wartość | 
    |---|---|---|
    |**Nazwa**|`NYCTaxiWorkspace1`|
    |**Nazwa obszaru roboczego**|`NYCTaxiWorkspace1`|
    |||
    
* Wybierz przycisk **Utwórz**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Tworzenie Power BI zestawu danych, który używa danych w obszarze roboczym Synapse

* W programie Synapse Studio przejdź do **Power BI tworzenia >**.
* Przejdź do **NYCTaxiWorkspace1 > Power BI zestawy** danych i wybierz pozycję **Nowy Power BI DataSet**.
* Umieść kursor nad bazą danych SQLDB1 i wybierz pozycję **Pobierz plik pbids**.
* Otwórz pobrany `.pbids` plik. Spowoduje to uruchomienie Power BI pulpicie i automatyczne połączenie go z usługą SQLDB1 w obszarze roboczym Synapse.
* Jeśli zostanie wyświetlone okno dialogowe o nazwie **baza danych programu SQL Server**:
    * Wybierz **konto Microsoft**. 
    * Wybierz pozycję **Zaloguj się** i zaloguj się.
    * Wybierz pozycję **Połącz**.
* Zostanie otwarte okno dialogowe **Nawigator** . Po zaznaczeniu pola wyboru **Załaduj**tabelę **PassengerCountStats** .
* Zostanie wyświetlone okno dialogowe **Ustawienia połączenia** . Wybierz **zapytanie** bezpośrednie i wybierz **przycisk OK**
* Wybierz przycisk **raportu** po lewej stronie.
* Dodaj **Wykres liniowy** do raportu.
    * Przeciągnij kolumnę **PasssengerCount** do **wizualizacji > osi**
    * Przeciągnij kolumny **SumTripDistance** i **AvgTripDistance** do **wizualizacji > wartości**.
* Na karcie **Narzędzia główne** wybierz pozycję **Publikuj**.
* Zostanie wyświetlony zapytanie, czy chcesz zapisać zmiany. Wybierz pozycję **Zapisz**.
* Zostanie wyświetlony zapytanie o wybranie nazwy pliku. Wybierz `PassengerAnalysis.pbix` i wybierz pozycję **Zapisz**.
* Zostanie wyświetlony zapytanie o **wybranie miejsca docelowego** i wybranie opcji `NYCTaxiWorkspace1` **Wybierz**.
* Zaczekaj na zakończenie publikowania.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurowanie uwierzytelniania dla zestawu danych

* Otwórz [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) i **Zaloguj się**
* Po lewej stronie w obszarze **obszary robocze** wybierz `NYCTaxiWorkspace1` obszar roboczy, który został opublikowany.
* Wewnątrz tego obszaru roboczego powinien zostać wyświetlony zestaw danych o nazwie `Passenger Analysis` i Raport `Passenger Analysis` .
* Umieść kursor nad `PassengerAnalysis` zestawem danych i wybierz ikonę z trzema kropkami, a następnie wybierz pozycję **Ustawienia**.
* W **poświadczeniach źródła danych** ustaw metodę uwierzytelniania na **OAuth2** i wybierz pozycję **Zaloguj**.

### <a name="edit-a-report-report-in-synapse-studio"></a>Edytowanie raportu raportu w programie Synapse Studio

* Wróć do Synapse Studio i wybierz pozycję **Zamknij i Odśwież** teraz, aby zobaczyć:
    * W obszarze **Power BI zbiory**danych nowy element dataset o nazwie **PassengerAnalysis**.
    * W obszarze **Power BI zestawy danych**nowy raport o nazwie **PassengerAnalysis**.
* Kliknij raport **PassengerAnalysis** . 
    * Nie pokazuje niczego, ponieważ nadal trzeba skonfigurować uwierzytelnianie dla zestawu danych.
* W programie SynapseStudio przejdź do **projektowania > Power BI > nazwę obszaru roboczego > Power BI raportów**.
* Zamknij wszystkie okna zawierające raport Power BI.
* Odśwież węzeł **raporty Power BI** .
* Wybierz raport, a teraz możesz edytować raport bezpośrednio w programie Synapse Studio.

## <a name="monitor-activities"></a>Monitorowanie działań

* W programie Synapse Studio przejdź do centrum monitora.
* W tej lokalizacji można zobaczyć historię wszystkich działań podejmowanych w obszarze roboczym, które są teraz aktywne.
* Eksploruj **przebiegi potokowe**, **Apache Spark aplikacje**i **żądania SQL** i możesz zobaczyć, co już zostało zrobione w obszarze roboczym.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Azure Synapse Analytics (wersja zapoznawcza)](overview-what-is.md)

