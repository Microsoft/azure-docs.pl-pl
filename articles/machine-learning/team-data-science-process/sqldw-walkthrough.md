---
title: Tworzenie i wdrażanie modelu przy użyciu usługi Azure Synapse Analytics — proces nauki o danych zespołowych
description: Tworzenie i wdrażanie modelu uczenia maszynowego przy użyciu usługi Azure Synapse Analytics z publicznie dostępnym zestawem danych.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9c4c1cfdb927cfd2ee607bfe2a951e06c80f9bfb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418545"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Proces nauki o danych zespołu w działaniu: korzystanie z usługi Azure Synapse Analytics
W tym samouczku prowadzimy użytkownika przez tworzenie i wdrażanie modelu uczenia maszynowego przy użyciu usługi Azure Synapse Analytics dla publicznie dostępnego zestawu danych — zestawu danych [NYC Taxi Trips.](https://www.andresmh.com/nyctaxitrips/) Skonstruowany model klasyfikacji binarnej przewiduje, czy wskazówka jest płatna za podróż.  Modele obejmują klasyfikację wieloklasową (niezależnie od tego, czy jest wskazówka) i regresję (rozkład dla zapłaconych kwot napiwków).

Procedura jest zgodna z przepływem pracy [proces nauki o danych zespołu (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) Pokazujemy, jak skonfigurować środowisko do nauki o danych, jak załadować dane do usługi Azure Synapse Analytics i jak używać usługi Azure Synapse Analytics lub notesu IPython do eksplorowania funkcji danych i inżynierów do modelowania. Następnie pokazujemy, jak skompilować i wdrożyć model za pomocą usługi Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>Zestaw danych NYC Taxi Trips
Dane NYC Taxi Trip składają się z około 20 GB skompresowanych plików CSV (~ 48 GB nieskompresowanych), rejestrując ponad 173 miliony pojedynczych podróży i opłaty zapłacone za każdą podróż. Każdy rekord podróży zawiera miejsca odbioru i nadsyłania, anonimowy numer licencji hack (kierowcy) i numer medalionu (unikalny identyfikator taksówki). Dane obejmują wszystkie podróże w 2013 r. i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

1. Plik **trip_data.csv** zawiera szczegóły podróży, takie jak liczba pasażerów, punkty odbioru i wysiadania, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Plik **trip_fare.csv** zawiera szczegółowe informacje o taryfie zapłaconej za każdą podróż, takie jak rodzaj płatności, kwota taryfy, dopłaty i podatki, napiwki i opłaty drogowe oraz całkowita zapłacona kwota. Oto kilka przykładowych rekordów:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Unikatowy klucz** używany\_do łączenia danych podróży i taryfy podróży\_składa się z następujących trzech pól:

* Medalion
* licencję hakerską\_i
* data\_odbioru.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Rozwiązywanie trzech typów zadań przewidywania
Sformułowaliśmy trzy problemy z przewidywaniem na podstawie *kwoty końcówki,\_* aby zilustrować trzy rodzaje zadań modelowania:

1. **Klasyfikacja binarna:** Aby przewidzieć, czy napiwek został zapłacony za podróż, czyli *kwota napiwku\_* większa niż $0 jest pozytywnym przykładem, podczas gdy kwota *napiwku\_* $0 jest przykładem ujemnym.
2. **Klasyfikacja wieloklasowa**: Aby przewidzieć zakres napiwku opłaconego za podróż. Dzielimy *ilość\_końcówki* na pięć pojemników lub klas:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Zadanie regresji:** Aby przewidzieć kwotę napiwku zapłaconego za podróż.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Konfigurowanie środowiska do nauki o danych platformy Azure do zaawansowanej analizy
Aby skonfigurować środowisko usługi Azure Data Science, wykonaj następujące kroki.

**Tworzenie własnego konta magazynu obiektów blob platformy Azure**

* Podczas aprowizowania własnego magazynu obiektów blob platformy Azure wybierz lokalizację geograficzną magazynu obiektów blob platformy Azure w magazynie obiektów blob platformy Azure lub jak najbliżej **południowo-środkowej części stanów USA,** czyli miejsca przechowywania danych taksówki NYC. Dane zostaną skopiowane przy użyciu AzCopy z kontenera magazynu obiektów blob publicznych do kontenera na własnym koncie magazynu. Im bliżej magazynu obiektów blob platformy Azure jest do południowo-środkowej stany USA, tym szybciej to zadanie (krok 4) zostanie ukończone.
* Aby utworzyć własne konto usługi Azure Storage, wykonaj kroki opisane w witrynie [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md). Pamiętaj, aby wprowadzać uwagi dotyczące wartości następujących poświadczeń konta magazynu, ponieważ będą one potrzebne w dalszej części tego przewodnika.

  * **Nazwa konta magazynu**
  * **Klucz konta magazynu**
  * **Nazwa kontenera** (który ma być przechowywany w magazynie obiektów blob platformy Azure)

**Aprowizuj swoje wystąpienie usługi Azure Synapse Analytics.**
Postępuj zgodnie z dokumentacją [w tworzenie i kwerendy usługi Azure SQL Data Warehouse w witrynie Azure portal](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) do inicjowania obsługi administracyjnej wystąpienia usługi Azure Synapse Analytics. Upewnij się, że należy wprowadzić notacje na następujące poświadczenia usługi Azure Synapse Analytics, które będą używane w kolejnych krokach.

* **Nazwa**serwera \<: nazwa serwera>.database.windows.net
* **Nazwa PROGRAMU SQLDW (database)**
* **Nazwę użytkownika**
* **Hasło**

**Zainstaluj narzędzia do pobierania danych w programie Visual Studio i SQL Server.** Aby uzyskać instrukcje, zobacz [Wprowadzenie do programu Visual Studio 2019 dla magazynu danych SQL.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)

**Połącz się z usługą Azure Synapse Analytics za pomocą programu Visual Studio.** Aby uzyskać instrukcje, zobacz kroki 1 & 2 w [połącz się z usługą SQL Analytics w usłudze Azure Synapse Analytics](../../synapse-analytics/sql/connect-overview.md).

> [!NOTE]
> Uruchom następującą kwerendę SQL w bazie danych utworzonej w usłudze Azure Synapse Analytics (zamiast kwerendy podanej w kroku 3 tematu połączenia), aby **utworzyć klucz główny**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Utwórz obszar roboczy usługi Azure Machine Learning w ramach subskrypcji platformy Azure.** Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](../studio/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Ładowanie danych do usługi Azure Synapse Analytics
Otwórz konsolę poleceń programu Windows PowerShell. Uruchom następujące polecenia programu PowerShell, aby pobrać przykładowe pliki skryptów SQL, które udostępniamy ci w usłudze GitHub, do katalogu lokalnego określonego za pomocą parametru *-DestDir*. Wartość parametru *-DestDir* można zmienić na dowolny katalog lokalny. Jeśli *-DestDir* nie istnieje, zostanie utworzony przez skrypt programu PowerShell.

> [!NOTE]
> Może być konieczne **uruchomienie jako administrator** podczas wykonywania następującego skryptu programu PowerShell, jeśli katalog *DestDir* wymaga uprawnień administratora do tworzenia lub zapisywania.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Po pomyślnym wykonaniu bieżący katalog roboczy zmienia się na *-DestDir*. Powinieneś być w stanie zobaczyć ekran jak poniżej:

![Bieżące zmiany w katalogu roboczym][19]

W *programie -DestDir*wykonaj następujący skrypt programu PowerShell w trybie administratora:

    ./SQLDW_Data_Import.ps1

Gdy skrypt programu PowerShell jest uruchamiany po raz pierwszy, zostaniesz poproszony o wprowadzenie informacji z usługi Azure Synapse Analytics i konta magazynu obiektów blob platformy Azure. Po zakończeniu pracy tego skryptu programu PowerShell wprowadzone poświadczenia zostaną zapisane w pliku konfiguracyjnym SQLDW.conf w obecnym katalogu roboczym. Przyszłe uruchomienie tego pliku skryptu programu PowerShell ma możliwość odczytu wszystkich potrzebnych parametrów z tego pliku konfiguracyjnego. Jeśli chcesz zmienić niektóre parametry, możesz wprowadzić parametry na ekranie po wierszu monitu, usuwając ten plik konfiguracyjny i wprowadzając wartości parametrów jako monit lub aby zmienić wartości parametrów, edytując plik SQLDW.conf w katalogu *-DestDir.*

> [!NOTE]
> Aby uniknąć konfliktów nazwy schematu z tymi, które już istnieją w usłudze Azure Azure Synapse Analytics, podczas odczytywania parametrów bezpośrednio z pliku SQLDW.conf, 3-cyfrowy numer losowy jest dodawany do nazwy schematu z pliku SQLDW.conf jako domyślna nazwa schematu dla każdego uruchomienia. Skrypt programu PowerShell może monitować o nazwę schematu: nazwa może być określona według uznania użytkownika.
>
>

Ten plik **skryptu programu PowerShell** wykona następujące zadania:

* **Pobiera i instaluje AzCopy**, jeśli AzCopy nie jest jeszcze zainstalowany

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Kopiuje dane do prywatnego konta magazynu obiektów blob** z publicznego obiektu blob za pomocą programu AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Ładuje dane przy użyciu polybase (wykonując LoadDataToSQLDW.sql) do usługi Azure Synapse Analytics** z konta magazynu obiektów blob z następujących poleceń.

  * Tworzenie schematu

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Tworzenie poświadczenia o zakresie bazy danych

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Tworzenie zewnętrznego źródła danych dla obiektu blob usługi Azure Storage

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * Utwórz format pliku zewnętrznego dla pliku csv. Dane są nieskompresowane, a pola są oddzielone znakiem potoku.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Tworzenie zewnętrznych tabel taryf i podróży dla zestawu danych taksówek NYC w magazynie obiektów blob platformy Azure.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - Ładowanie danych z tabel zewnętrznych w magazynie obiektów blob platformy Azure do usługi Azure Synapse Analytics

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Utwórz przykładową tabelę danych (NYCTaxi_Sample) i wstaw do niej dane, wybierając zapytania SQL w tabelach podróży i taryf. (Niektóre kroki tego przewodnika należy użyć tej tabeli przykładowej.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

Lokalizacja geograficzna kont magazynu wpływa na czasy ładowania.

> [!NOTE]
> W zależności od lokalizacji geograficznej konta magazynu obiektów blob proces kopiowania danych z publicznego obiektu blob do konta magazynu prywatnego może potrwać około 15 minut, a nawet dłużej, a proces ładowania danych z konta magazynu do usługi Azure Synapse Analytics może potrwać 20 minut lub dłużej.
>
>

Będziesz musiał zdecydować, co zrobić, jeśli masz zduplikowane pliki źródłowe i docelowe.

> [!NOTE]
> Jeśli pliki csv, które mają zostać skopiowane z publicznego magazynu obiektów blob do prywatnego konta magazynu obiektów blob, już istnieją na koncie prywatnego magazynu obiektów blob, program AzCopy zapyta, czy chcesz je zastąpić. Jeśli nie chcesz ich zastąpić, po wyświetleniu monitu wprowadź **n.** Jeśli chcesz zastąpić **wszystkie** z nich, wprowadź **po** wyświetleniu monitu. Można również wprowadzić **y,** aby zastąpić pliki csv indywidualnie.
>
>

![Wyjście z AzCopy][21]

Możesz użyć własnych danych. Jeśli dane są na komputerze lokalnym w aplikacji rzeczywistych, nadal można użyć AzCopy do przekazywania danych lokalnych do prywatnego magazynu obiektów blob platformy Azure. Wystarczy zmienić lokalizację **źródłową,** `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`w poleceniu AzCopy pliku skryptu programu PowerShell na katalog lokalny zawierający dane.

> [!TIP]
> Jeśli dane są już w prywatnym magazynie obiektów blob platformy Azure w aplikacji rzeczywistych, można pominąć krok AzCopy w skrypcie programu PowerShell i bezpośrednio przekazać dane do usługi Azure Azure Synapse Analytics. Będzie to wymagało dodatkowych zmian skryptu, aby dostosować go do formatu danych.
>
>

Ten skrypt programu PowerShell również podłącza informacje usługi Azure Synapse Analytics do przykładowych plików eksploracji danych SQLDW_Explorations.sql, SQLDW_Explorations.ipynb i SQLDW_Explorations_Scripts.py, dzięki czemu te trzy pliki są gotowe do wypróbowania natychmiast po zakończeniu skryptu programu PowerShell.

Po pomyślnym wykonaniu, zobaczysz ekran jak poniżej:

![Dane wyjściowe pomyślnego wykonania skryptu][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Eksploracja danych i inżynieria funkcji w usłudze Azure Synapse Analytics
W tej sekcji wykonujemy eksplorację danych i generowanie funkcji, uruchamiając zapytania SQL względem usługi Azure Synapse Analytics bezpośrednio przy użyciu **narzędzi danych programu Visual Studio.** Wszystkie kwerendy SQL używane w tej sekcji można znaleźć w przykładowym skrypcie o nazwie *SQLDW_Explorations.sql*. Ten plik został już pobrany do katalogu lokalnego przez skrypt programu PowerShell. Można go również pobrać z [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ale plik w usłudze GitHub nie ma podłączonych informacji usługi Azure Synapse Analytics.

Połącz się z usługą Azure Synapse Analytics przy użyciu programu Visual Studio za pomocą nazwy logowania i hasła usługi Azure Synapse Analytics i otwórz **Eksploratora obiektów SQL,** aby potwierdzić, że baza danych i tabele zostały zaimportowane. Pobierz plik *SQLDW_Explorations.sql.*

> [!NOTE]
> Aby otworzyć edytor zapytań magazynu danych równoległych (PDW), użyj polecenia **Nowa kwerenda,** gdy plik PDW jest zaznaczony w **Eksploratorze obiektów SQL**. Standardowy edytor zapytań SQL nie jest obsługiwany przez plik PDW.
>
>

Oto typy zadań eksploracji danych i generowania funkcji wykonywanych w tej sekcji:

* Eksploruj dystrybucje danych w kilku polach w różnych oknach czasowych.
* Zbadaj jakość danych pól długości i szerokości geograficznej.
* Generowanie etykiet klasyfikacji binarnej i wieloklasowej na podstawie **kwoty końcówki\_**.
* Generuj funkcje i obliczaj/porównuj odległości podróży.
* Dołącz do dwóch tabel i wyodrębnić losową próbkę, która będzie używana do tworzenia modeli.

### <a name="data-import-verification"></a>Weryfikacja importu danych
Te zapytania zapewniają szybką weryfikację liczby wierszy i kolumn w tabelach wypełnionych wcześniej przy użyciu importu zbiorczego polybase,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Wyjście:** Powinieneś dostać 173,179,759 wiersze i 14 kolumn.

### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Dystrybucja podróży przez medalion
W tym przykładzie kwerendy identyfikuje medaliony (numery taksówek), które ukończyły więcej niż 100 podróży w określonym okresie czasu. Kwerenda będzie korzystać z podzielonego na partycje dostępu do tabeli, ponieważ jest to uwarunkowane przez schemat partycji **datatime odbioru\_**. Kwerenda pełnego zestawu danych będzie również korzystać z partycjonowanych tabeli i/lub skanowania indeksu.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Wyjście:** Kwerenda powinna zwracać tabelę z wierszami określającymi 13 369 medalionów (taksówek) i liczbę podróży ukończonych w 2013 roku. Ostatnia kolumna zawiera liczbę ukończonych podróży.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Eksploracja: Dystrybucja podróży przez medalion i hack_license
W tym przykładzie identyfikuje medaliony (numery taksówek) i hack_license numerów (kierowców), które ukończyły ponad 100 przejazdów w określonym przedziale czasu.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Wyjście:** Kwerenda powinna zwrócić tabelę z 13 369 wierszami określającymi 13 369 identyfikatorów samochodów/kierowców, które ukończyły ponad 100 przejazdów w 2013 roku. Ostatnia kolumna zawiera liczbę ukończonych podróży.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Ocena jakości danych: weryfikuj rekordy z nieprawidłową długością i/lub szerokością geograficzną
W tym przykładzie bada, czy którykolwiek z pól długości i/lub szerokości geograficznej albo zawierają nieprawidłową wartość (promieniowe stopnie powinny być między -90 i 90), lub mają (0, 0) współrzędne.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Wyjście:** Kwerenda zwraca 837 467 podróży, które mają nieprawidłowe pola długości i/lub szerokości geograficznej.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Eksploracja: Przechylony a nie przechylony dystrybucja podróży
W tym przykładzie znajduje się liczba podróży, które zostały przechylone w porównaniu z liczbą, które nie zostały przechylone w określonym przedziale czasu (lub w pełnym zestawie danych, jeśli obejmuje cały rok, jak to jest skonfigurowane w tym miejscu). Ta dystrybucja odzwierciedla dystrybucji etykiet binarnych, które mają być później używane do modelowania klasyfikacji binarnej.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Wyjście:** Kwerenda powinna zwracać następujące częstotliwości porad dla roku 2013: 90,447,622 przechylony i 82,264,709 nie-tipped.

### <a name="exploration-tip-classrange-distribution"></a>Eksploracja: Klasa końcówka/rozkład zasięgu
W tym przykładzie oblicza rozkład zakresów końcówek w danym okresie czasu (lub w pełnym zestawie danych, jeśli obejmuje cały rok). Ten rozkład klas etykiet będzie używany później do modelowania klasyfikacji wieloklasowej.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Wyjście:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Eksploracja: Oblicz i porównaj odległość podróży
W tym przykładzie konwertuje długość i szerokość geograficzną odbioru i dropoff do punktów geografii SQL, oblicza odległość podróży przy użyciu różnicy punktów geografii SQL i zwraca losową próbkę wyników do porównania. Przykład ogranicza wyniki do prawidłowych współrzędnych tylko przy użyciu kwerendy oceny jakości danych omówione wcześniej.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Inżynieria funkcji przy użyciu funkcji SQL
Czasami funkcje SQL może być skuteczną opcją dla inżynierii funkcji. W tym instruktażu zdefiniowaliśmy funkcję SQL, aby obliczyć bezpośrednią odległość między lokalizacjami odbioru i dropoff. W **programie Visual Studio Data Tools**można uruchomić następujące skrypty SQL.

Oto skrypt SQL, który definiuje funkcję odległości.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Oto przykład wywołania tej funkcji w celu wygenerowania funkcji w kwerendzie SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Wyjście:** Ta kwerenda generuje tabelę (z 2 803 538 wierszy) z szerokościami i długościami i długościami odbioru i dropoff oraz odpowiadającymi im bezpośrednimi odległościami w milach. Oto wyniki dla pierwszych trzech wierszy:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | Bezpośrednia dyskutka |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Przygotowywanie danych do budowania modelu
Poniższa kwerenda łączy **tabele\_taryf nyctaxi** i **nyctaxi,\_** generuje etykietę klasyfikacji binarnej z **podziałką**, klasę etykiet **\_** klasy wieloklasowej i wyodrębnia próbkę z pełnego zestawu danych. Próbkowanie odbywa się przez pobranie podzbioru podróży na podstawie czasu odbioru.  Ta kwerenda może być kopiowana, a następnie wklejona bezpośrednio w module [importu danych][importu] danych usługi Azure Machine Learning [Studio (klasyczny)](https://studio.azureml.net) w celu bezpośredniego pozyskiwania danych z wystąpienia bazy danych SQL na platformie Azure. Kwerenda wyklucza rekordy z niepoprawnymi współrzędnymi (0, 0).

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Gdy będziesz gotowy, aby przejść do usługi Azure Machine Learning, możesz:

1. Zapisz ostateczną kwerendę SQL, aby wyodrębnić i próbkować dane oraz skopiować i wkleić kwerendę bezpośrednio do modułu[importu danych importu] danych w usłudze Azure Machine Learning lub
2. Utrwalić próbkowane i zaprojektowane dane, których zamierzasz użyć do tworzenia modelu w nowej tabeli usługi Azure Synapse Analytics i użyj nowej tabeli w module [Importuj dane][importu danych] w usłudze Azure Machine Learning. Skrypt programu PowerShell we wcześniejszym kroku wykonał to zadanie za Ciebie. Można odczytać bezpośrednio z tej tabeli w module Importuj dane.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Eksploracja danych i inżynieria funkcji w notebooku IPython
W tej sekcji będziemy wykonywać eksploracji danych i generowania funkcji przy użyciu zapytań Języka Python i SQL względem usługi Azure Synapse Analytics utworzone wcześniej. Przykładowy notes IPython o nazwie **SQLDW_Explorations.ipynb** i plik skryptu **Pythona SQLDW_Explorations_Scripts.py** zostały pobrane do lokalnego katalogu. Są one również dostępne na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Te dwa pliki są identyczne w skryptach Pythona. Plik skryptu Pythona jest dostarczany w przypadku, gdy nie masz serwera notebooka IPython. Te dwa przykładowe pliki Pythona są zaprojektowane pod **Python 2.7**.

Potrzebne informacje usługi Azure Synapse Analytics w przykładowym notesie IPython i pliku skryptu Języka Python pobranego na komputer lokalny zostały wcześniej podłączone przez skrypt programu PowerShell. Są one wykonywalne bez żadnych modyfikacji.

Jeśli masz już skonfigurowany obszar roboczy usługi Azure Machine Learning, możesz bezpośrednio przekazać przykładowy notes IPython do usługi AzureML IPython Notebook i rozpocząć jego uruchamianie. Oto kroki, które należy przekazać do usługi AzureML IPython Notebook usługi:

1. Zaloguj się do obszaru roboczego usługi Azure Machine Learning, kliknij studio **u** góry i kliknij pozycję **NOTESY** po lewej stronie strony sieci Web.

    ![Kliknij pozycję Studio, a następnie POZYCJĘ NOTESS][22]
2. Kliknij **przycisk NOWY** w lewym dolnym rogu strony internetowej, a następnie wybierz pozycję Python **2**. Następnie podaj nazwę notesu i kliknij znacznik wyboru, aby utworzyć nowy pusty notes IPython.

    ![Kliknij przycisk NOWY, a następnie wybierz python 2][23]
3. Kliknij symbol **Jupyter** w lewym górnym rogu nowego notesu IPython.

    ![Kliknij symbol Jupyter][24]
4. Przeciągnij i upuść przykładowy notes IPython na stronę **drzewa** usługi notesu usługi AzureML IPython, a następnie kliknij przycisk **Przekaż**. Następnie przykładowy notes IPython zostanie przekazany do usługi Notesie usługi AzureML IPython.

    ![Kliknij przycisk Przekaż][25]

Aby uruchomić przykładowy notes IPython lub plik skryptu Pythona, potrzebne są następujące pakiety Pythona. Jeśli używasz usługi AzureML IPython Notebook usługi, te pakiety zostały wstępnie zainstalowane.

- Pandy
- Numpy
- matplotlib
- pyodbc
- PyTables (Tabele pytables)

Podczas tworzenia zaawansowanych rozwiązań analitycznych w usłudze Azure Machine Learning z dużymi danymi, oto zalecana sekwencja:

* Odczyt w małej próbce danych do ramki danych w pamięci.
* Wykonaj niektóre wizualizacje i eksploracje przy użyciu próbkowania danych.
* Eksperymentuj z inżynierią funkcji przy użyciu próbkowania danych.
* Aby uzyskać większe eksploracji danych, manipulowania danymi i inżynierii funkcji, użyj języka Python do wystawiania zapytań SQL bezpośrednio względem usługi Azure Synapse Analytics.
* Zdecyduj rozmiar próbki, aby był odpowiedni dla tworzenia modelu usługi Azure Machine Learning.

Oto kilka eksploracji danych, wizualizacji danych i przykładów inżynierii funkcji. Więcej eksploracji danych można znaleźć w przykładowym notesie IPython i przykładowym pliku skryptu Języka Python.

### <a name="initialize-database-credentials"></a>Inicjowanie poświadczeń bazy danych
Zainicjowanie ustawień połączenia z bazą danych w następujących zmiennych:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Tworzenie połączenia z bazą danych
Oto parametry połączenia, który tworzy połączenie z bazą danych.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Raport liczbę wierszy i kolumn w tabeli <nyctaxi_trip>
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Całkowita liczba wierszy = 173179759
* Całkowita liczba kolumn = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Raport liczbę wierszy i kolumn w tabeli <nyctaxi_fare>
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Całkowita liczba wierszy = 173179759
* Całkowita liczba kolumn = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Odczyt małego przykładu danych z bazy danych usługi Azure Synapse Analytics
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Czas odczytu przykładowej tabeli to 14.096495 sekund.
Liczba pobranych wierszy i kolumn = (1000, 21).

### <a name="descriptive-statistics"></a>Statystyki opisowe
Teraz możesz przystąpić do eksplorowania próbkowania danych. Zaczynamy od przyjrzenia się niektórym statystykom opisowym **dla odległości podróży\_** (lub innych pól, które określisz).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Wizualizacja: przykład wykresu pola
Następnie patrzymy na wykres polu dla odległości podróży do wizualizacji quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Wyjście wykresu pola][1]

### <a name="visualization-distribution-plot-example"></a>Wizualizacja: przykład wykresu dystrybucji
Wykresy, które wizualizują rozkład i histogram dla badanych odległości podróży.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Wyjście wykresu dystrybucji][2]

### <a name="visualization-bar-and-line-plots"></a>Wizualizacja: wykresy słupkowe i liniowe
W tym przykładzie możemy bin odległość podróży do pięciu pojemników i wizualizacji wyników binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Możemy wykreślić powyższy rozkład pojemnika na pasku lub na wykresie liniowym z:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Wyjście wykresu prętowego][3]

i

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Wyjście wykresu linii][4]

### <a name="visualization-scatterplot-examples"></a>Wizualizacja: przykłady scatterplot
Pokazujemy wykres punktowy między **czasem\_\_podróży w\_sekundach** a **odległością podróży,\_** aby sprawdzić, czy istnieje korelacja

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Wyjście scatterplot relacji między czasem a odległością][6]

Podobnie możemy sprawdzić związek między **kodem stawki\_** a **odległością podróży.\_**

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Wyjście scatterplot relacji między kodem a odległością][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Eksploracja danych na przykładowych danych przy użyciu zapytań SQL w notesie IPython
W tej sekcji firma We eksplorujemy dystrybucje danych przy użyciu próbkowanych danych, które są zachowywane w nowej tabeli, którą utworzyliśmy powyżej. Podobne eksploracje mogą być wykonywane przy użyciu oryginalnych tabel.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Eksploracja: Zgłoś liczbę wierszy i kolumn w próbkowanej tabeli
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Eksploracja: Przechylony / nie potknięty dystrybucja
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Eksploracja: Dystrybucja klas porad
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Eksploracja: Wykreślić rozkład końcówki według klasy
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 działki][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Eksploracja: Codzienna dystrybucja podróży
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Eksploracja: Dystrybucja podróży na medalion
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Eksploracja: Dystrybucja podróży przez medalion i licencję hack
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Eksploracja: Dystrybucja czasu podróży
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Eksploracja: Rozkład odległości podróży
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Eksploracja: Dystrybucja typu płatności
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Sprawdź ostateczną formę tabeli featurized
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Tworzenie modeli w usłudze Azure Machine Learning
Jesteśmy teraz gotowi przystąpić do tworzenia modelu i wdrażania modelu w [usłudze Azure Machine Learning.](https://studio.azureml.net) Dane są gotowe do użycia w każdym z problemów z przewidywaniem zidentyfikowanych wcześniej, a mianowicie:

1. **Klasyfikacja binarna**: Aby przewidzieć, czy napiwek został opłacony za podróż.
2. **Klasyfikacja wieloklasowa**: Aby przewidzieć zakres końcówki wypłacane, zgodnie z wcześniej zdefiniowanych klas.
3. **Zadanie regresji:** Aby przewidzieć kwotę napiwku zapłaconego za podróż.

Aby rozpocząć ćwiczenie modelowania, zaloguj się do swojego obszaru **roboczego usługi Azure Machine Learning (klasyczny).** Jeśli nie utworzono jeszcze obszaru roboczego uczenia maszynowego, zobacz [Tworzenie obszaru roboczego Usługi Azure Machine Learning Studio (klasycznego).](../studio/create-workspace.md)

1. Aby rozpocząć korzystanie z usługi Azure Machine Learning, zobacz [Co to jest usługa Azure Machine Learning Studio (klasyczna)?](../studio/what-is-ml-studio.md)
2. Zaloguj się do [usługi Azure Machine Learning Studio (klasyczny)](https://studio.azureml.net).
3. Machine Learning Studio (klasyczny) Strona główna zawiera wiele informacji, filmy, samouczki, łącza do modułów odniesienia i innych zasobów. Aby uzyskać więcej informacji na temat usługi Azure Machine Learning, zobacz [Centrum dokumentacji usługi Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typowy eksperyment szkoleniowy składa się z następujących kroków:

1. Utwórz **eksperyment +NOWY.**
2. Pobierz dane do usługi Azure Machine Learning Studio (klasyczny).
3. W razie potrzeby przetwórz, przekształcaj i manipuluj danymi.
4. Generuj funkcje w razie potrzeby.
5. Podziel dane na zestawy danych szkoleniowe/walidacyjne/testujące(lub mają oddzielne zestawy danych dla każdego z nich).
6. Wybierz jeden lub więcej algorytmów uczenia maszynowego w zależności od problemu uczenia się do rozwiązania. Na przykład klasyfikacja binarna, klasyfikacja wieloklasowa, regresja.
7. Trenuj jeden lub więcej modeli przy użyciu zestawu danych szkolenia.
8. Ocena zestawu danych sprawdzania poprawności przy użyciu wyszkolonych modeli.
9. Oceń model(y), aby obliczyć odpowiednie metryki problemu uczenia się.
10. Dostroić model(y) i wybrać najlepszy model do wdrożenia.

W tym ćwiczeniu zbadaliśmy już i zaprojektowaliśmy dane w usłudze Azure Synapse Analytics i zdecydowaliśmy się na przykładowy rozmiar do pozyskiwania w usłudze Azure Machine Learning Studio (klasyczny). Oto procedura tworzenia jednego lub więcej modeli przewidywania:

1. Pobierz dane do usługi Azure Machine Learning Studio (klasyczny) przy użyciu [importu danych][importu danych] modułu, dostępne w danych **wejściowych i wyjściowych** sekcji. Aby uzyskać więcej informacji, zobacz stronę odwołania do[modułu importu danych importuj] dane. [Import Data]

    ![Dane importu usługi Azure ML][17]
2. Wybierz **usługę Azure SQL Database** jako źródło **danych** w panelu **Właściwości.**
3. Wprowadź nazwę DNS bazy danych w polu **Nazwa serwera bazy danych.** Formacie:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Wprowadź **nazwę bazy danych** w odpowiednim polu.
5. Wprowadź *nazwę użytkownika SQL* w **nazwie konta użytkownika serwera**oraz *hasło* w **haśle konta użytkownika serwera**.
7. W obszarze edycji tekstu **kwerendy bazy danych** wklej kwerendę, która wyodrębnia niezbędne pola bazy danych (w tym wszystkie obliczone pola, takie jak etykiety) i w dół próbkuje dane do żądanego rozmiaru próbki.

Przykład eksperymentu klasyfikacji binarnej odczytu danych bezpośrednio z bazy danych usługi Azure Synapse Analytics znajduje się na rysunku poniżej (należy pamiętać, aby zastąpić nazwy tabel nyctaxi_trip i nyctaxi_fare przez nazwę schematu i nazwy tabel używane w instruktażu). Podobne eksperymenty mogą być konstruowane dla wieloklasowych problemów klasyfikacji i regresji.

![Pociąg usługi Azure ML][10]

> [!IMPORTANT]
> W przykładach wyodrębniania danych modelowania i próbkowania zapytań podanych w poprzednich sekcjach **wszystkie etykiety dla trzech ćwiczeń modelowania są zawarte w kwerendzie**. Ważnym (wymaganym) krokiem w każdym z ćwiczeń modelowania jest **wykluczenie** zbędnych etykiet dla pozostałych dwóch problemów i wszelkich innych **przecieków docelowych.** Na przykład w przypadku klasyfikacji binarnej należy użyć etykiety **z podziałką** i wykluczyć **klasę końcówki\_** pól, kwotę **\_końcówki**i łączną **\_kwotę.** Te ostatnie są przecieki docelowe, ponieważ implikują napiwek wypłacane.
>
> Aby wykluczyć niepotrzebne kolumny lub przecieki docelowe, można użyć modułu [Wybierz kolumny w][select-columns] zestawie danych lub Edytuj [metadane][edit-metadata]. Aby uzyskać więcej informacji, zobacz [Wybieranie kolumn w zestawie danych][select-columns] i Edytowanie stron referencyjnych [metadanych.][edit-metadata]
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Wdrażanie modeli w usłudze Azure Machine Learning
Gdy model jest gotowy, można łatwo wdrożyć go jako usługę sieci web bezpośrednio z eksperymentu. Aby uzyskać więcej informacji na temat wdrażania usług sieci Web usługi Azure ML, zobacz [Wdrażanie usługi sieci Web usługi Azure Machine Learning.](../studio/deploy-a-machine-learning-web-service.md)

Aby wdrożyć nową usługę sieci web, należy:

1. Tworzenie eksperymentu oceniania.
2. Wdrażanie usługi sieci web.

Aby utworzyć eksperyment oceniania z eksperymentu **Zakończone szkolenie,** kliknij przycisk **UTWÓRZ EKSPERYMENT PUNKTACJI** na dolnym pasku akcji.

![Ocena platformy Azure][18]

Usługa Azure Machine Learning podejmie próbę utworzenia eksperymentu oceniania na podstawie składników eksperymentu szkoleniowego. W szczególności:

1. Zapisz przeszkolony model i usuń moduły szkoleniowe modelu.
2. Identyfikowanie logicznego **portu wejściowego** w celu reprezentowania oczekiwanego schematu danych wejściowych.
3. Identyfikowanie logicznego **portu wyjściowego** w celu reprezentowania oczekiwanego schematu wyjściowego usługi sieci Web.

Podczas tworzenia eksperymentu oceniania przejrzyj wyniki i dostosuj się zgodnie z potrzebami. Typowym dostosowaniem jest zastąpienie wejściowego zestawu danych lub kwerendy takim, który wyklucza pola etykiet, ponieważ te pola etykiet nie będą mapowane do schematu podczas wywoływania usługi. Jest również dobrą praktyką, aby zmniejszyć rozmiar wejściowego zestawu danych i/lub kwerendy do kilku rekordów, wystarczy wskazać schemat wejściowy. Dla portu wyjściowego jest wspólne, aby wykluczyć wszystkie pola wejściowe i obejmują tylko **ocenione etykiety** i **ocenione prawdopodobieństwa** w danych wyjściowych przy użyciu Wybierz kolumny w module [zestawu danych.][select-columns]

Przykładowy eksperyment oceniania znajduje się na poniższym rysunku. Gdy będzie gotowy do wdrożenia, kliknij przycisk **PUBLIKUJ USŁUGĘ SIECI WEB** na dolnym pasku akcji.

![Publikowanie usługi Azure ML][11]

## <a name="summary"></a>Podsumowanie
Aby podsumować, co zrobiliśmy w tym samouczku, utworzono środowisko nauki o danych platformy Azure, pracował z dużym publicznym zestawem danych, biorąc go za pośrednictwem procesu nauki o danych zespołu, aż do pozyskiwania danych do szkolenia modelu, a następnie do wdrożenia usługi sieci web usługi Azure Machine Learning.

### <a name="license-information"></a>Informacje o licencji
Ten przykładowy instruktaż i towarzyszące mu skrypty i notesy IPython są udostępniane przez firmę Microsoft na licencji MIT. Sprawdź plik LICENSE.txt w katalogu przykładowego kodu w usłudze GitHub, aby uzyskać więcej informacji.

## <a name="references"></a>Dokumentacja
- [Andrés Monroy NYC Taxi Trips Strona pobierania](https://www.andresmh.com/nyctaxitrips/)
- [FOILing NYC's Taxi Trip Dane Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC Taxi i Limuzyny Komisji Badania i Statystyki](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
