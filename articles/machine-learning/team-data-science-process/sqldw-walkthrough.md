---
title: Kompilowanie i wdrażanie modelu przy użyciu usługi Azure Synapse Analytics — proces nauki danych zespołu
description: Kompiluj i wdrażaj model uczenia maszynowego przy użyciu usługi Azure Synapse Analytics z publicznie dostępnym zestawem danych.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e48261c4c6aeb75556663e1bf77c675557bcd1b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315494"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Proces nauki danych zespołu w działaniu: korzystanie z usługi Azure Synapse Analytics
W ramach tego samouczka przeprowadzimy Cię przez proces tworzenia i wdrażania modelu uczenia maszynowego przy użyciu usługi Azure Synapse Analytics dla publicznie dostępnego zestawu danych — zestawu danych [podróży NYC z taksówkami](https://www.andresmh.com/nyctaxitrips/) . Model klasyfikacji binarnej skonstruowany przewiduje, czy Porada jest płatna za podróż.  Modele obejmują klasyfikację wieloklasową (bez względu na to, czy jest to Porada) i regresję (dystrybucja dla płatnych sum).

Procedura jest zgodna z przepływem pracy [zespołowej analizy danych (przetwarzania TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) . Pokazujemy, jak skonfigurować środowisko do nauki o danych, jak załadować dane do usługi Azure Synapse Analytics oraz jak korzystać z usługi Azure Synapse Analytics lub notesu IPython w celu eksplorowania funkcji danych i inżynierów z modelem. Następnie pokazano, jak skompilować i wdrożyć model przy użyciu Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>Zestaw danych podróży z NYC taksówkami
Dane dotyczące podróży z taksówką NYC obejmują około 20 GB skompresowanych plików CSV (~ 48 GB nieskompresowanych), nagrywanie ponad 173 000 000 pojedynczych podróży i opłat za każdą podróż. Każdy rekord rejsu zawiera lokalizację i czas odbioru oraz Dropoff, a także numer licencji () i Medallion (unikatowy identyfikator taksówki). Dane obejmują wszystkie podróże w roku 2013 i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

1. Plik **trip_data.csv** zawiera szczegółowe informacje o podróży, takie jak liczba pasażerów, punkty odbioru i Dropoff, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Plik **trip_fare.csv** zawiera szczegółowe informacje o opłatach za każdą podróż, takie jak typ płatności, kwota opłaty, opłata dodatkowa i podatki, porady i opłaty, a także łączną kwotę płatną. Oto kilka przykładowych rekordów:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

**Unikatowy klucz** służący do przyłączania \_ danych podróży i opłaty za podróż \_ są złożone z następujących trzech pól:

* medallion,
* \_Licencjonowanie licencji i
* \_Data i godzina pobrania.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Adresowanie trzech typów zadań przewidywania
Opracowujemy trzy problemy z przewidywaniami na podstawie * \_ kwoty* pozostałej do zilustrowania trzech rodzajów zadań modelowania:

1. **Klasyfikacja binarna**: w celu przewidywania, czy Porada została zapłacona dla podróży, to oznacza, *że \_ Kwota TIP* , która jest większa niż $0, jest dodatnim przykładem, podczas gdy * \_ Kwota* pozostała do $0 jest ujemna.
2. **Klasyfikacja wieloklasowa**: przewidywanie zakresu porady dla podróży. Podziel * \_ kwotę* na pięć przedziałów lub klas:

`Class 0 : tip_amount = $0`

`Class 1 : tip_amount > $0 and tip_amount <= $5`

`Class 2 : tip_amount > $5 and tip_amount <= $10`

`Class 3 : tip_amount > $10 and tip_amount <= $20`

`Class 4 : tip_amount > $20`

3. **Zadanie regresji**: przewidywanie kwoty Porada płatnej dla podróży.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Konfigurowanie środowiska nauki o danych Azure na potrzeby zaawansowanej analizy
Aby skonfigurować środowisko nauki danych platformy Azure, wykonaj następujące kroki.

**Tworzenie własnego konta usługi Azure Blob Storage**

* Podczas aprowizacji własnego magazynu obiektów blob platformy Azure wybierz lokalizację geograficzną magazynu obiektów blob platformy Azure w lub jak najbliżej **Południowo-środkowe stany USA**, czyli miejsce, w którym są przechowywane dane z NYC. Dane zostaną skopiowane za pomocą AzCopy z publicznego kontenera magazynu obiektów BLOB do kontenera na własnym koncie magazynu. Bliżej magazynu obiektów blob platformy Azure to Południowo-środkowe stany USA, tym szybciej to zadanie (krok 4) zostanie zakończone.
* Aby utworzyć własne konto usługi Azure Storage, wykonaj czynności opisane w sekcji [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md). Pamiętaj, aby tworzyć notatki dotyczące następujących poświadczeń konta magazynu, ponieważ będą one potrzebne w dalszej części tego instruktażu.

  * **Nazwa konta magazynu**
  * **Klucz konta magazynu**
  * **Nazwa kontenera** (w którym dane mają być przechowywane w usłudze Azure Blob Storage)

**Inicjowanie obsługi administracyjnej wystąpienia usługi Azure Synapse Analytics.**
Postępuj zgodnie z dokumentacją w temacie [Tworzenie i wykonywanie zapytań dotyczących usługi Azure Synapse Analytics w Azure Portal,](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) aby udostępnić wystąpienie usługi Azure Synapse Analytics. Upewnij się, że wprowadzono notację na następujących poświadczeniach usługi Azure Synapse Analytics, które będą używane w dalszych krokach.

* **Nazwa serwera**: \<server Name> . Database.Windows.NET
* **Nazwa SQLDW (baza danych)**
* **Nazwa użytkownika**
* **Hasło**

**Zainstaluj program Visual Studio i narzędzia danych SQL Server.** Aby uzyskać instrukcje, zobacz [Rozpoczynanie pracy z programem Visual Studio 2019 for Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Połącz się z usługą Azure Synapse Analytics przy użyciu programu Visual Studio.** Aby uzyskać instrukcje, zobacz kroki 1 & 2 w artykule [nawiązywanie połączenia z usługą SQL Analytics w usłudze Azure Synapse Analytics](../../synapse-analytics/sql/connect-overview.md).

> [!NOTE]
> Uruchom następujące zapytanie SQL w bazie danych utworzonej w usłudze Azure Synapse Analytics (zamiast zapytania podanego w kroku 3 tematu Połącz), aby **utworzyć klucz główny**.
>
>

```sql
BEGIN TRY
       --Try to create the master key
    CREATE MASTER KEY
END TRY
BEGIN CATCH
       --If the master key exists, do nothing
END CATCH;
```

**Utwórz obszar roboczy Azure Machine Learning w ramach subskrypcji platformy Azure.** Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](../classic/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Ładowanie danych do usługi Azure Synapse Analytics
Otwórz konsolę poleceń programu Windows PowerShell. Uruchom następujące polecenia programu PowerShell, aby pobrać przykładowe pliki skryptów SQL udostępniane w usłudze GitHub do katalogu lokalnego określonego za pomocą parametru *-DESTDIR*. Wartość parametru *-DESTDIR* można zmienić na dowolny katalog lokalny. Jeśli *DESTDIR* nie istnieje, zostanie utworzony przez skrypt programu PowerShell.

> [!NOTE]
> Może być konieczne **uruchomienie programu jako administrator** podczas wykonywania poniższego skryptu programu PowerShell, jeśli katalog *DESTDIR* wymaga uprawnień administratora w celu utworzenia lub zapisu w nim.
>
>

```azurepowershell
$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
$wc = New-Object System.Net.WebClient
$wc.DownloadFile($source, $ps1_dest)
.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'
```

Po pomyślnym wykonaniu bieżący katalog roboczy zmieni się na *-DESTDIR*. Powinien być widoczny ekran podobny do następującego:

![Bieżące zmiany w katalogu roboczym][19]

W *DESTDIR*, wykonaj następujący skrypt programu PowerShell w trybie administratora:

```azurepowershell
./SQLDW_Data_Import.ps1
```

Gdy skrypt programu PowerShell jest uruchamiany po raz pierwszy, zostanie wyświetlony monit o wprowadzenie informacji z usługi Azure Synapse Analytics i konta usługi Azure Blob Storage. Gdy ten skrypt programu PowerShell zostanie uruchomiony po raz pierwszy, wprowadzone poświadczenia zostaną zapisaną w pliku konfiguracji SQLDW. conf w obecnym katalogu roboczym. W przyszłości uruchomienie tego pliku skryptu programu PowerShell umożliwia odczytanie wszystkich wymaganych parametrów z tego pliku konfiguracji. Jeśli musisz zmienić niektóre parametry, możesz wybrać opcję wprowadzania parametrów na ekranie po wyświetleniu monitu, usuwając ten plik konfiguracji i wprowadzając wartości parametrów jako monit lub aby zmienić wartości parametrów, edytując plik SQLDW. conf w katalogu *-DESTDIR* .

> [!NOTE]
> Aby uniknąć konfliktów nazw schematu z tymi, które już istnieją w usłudze Azure Synapse Analytics, podczas odczytywania parametrów bezpośrednio z pliku SQLDW. conf do nazwy schematu z pliku SQLDW. conf jest dodawana 3-cyfrowa liczba losowa. Skrypt programu PowerShell może monitować o nazwę schematu: nazwa może być określona według uznania użytkownika.
>
>

Ten plik **skryptu programu PowerShell** wykonuje następujące zadania:

* **Pobiera i instaluje AzCopy**, jeśli AzCopy nie jest jeszcze zainstalowany

  ```azurepowershell
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
  ```

* **Kopiuje dane do prywatnego konta usługi BLOB Storage** z publicznego obiektu BLOB za pomocą AzCopy

  ```azurepowershell
  Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
  $start_time = Get-Date
  AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
  $end_time = Get-Date
  $time_span = $end_time - $start_time
  $total_seconds = [math]::Round($time_span.TotalSeconds,2)
  Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
  Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
  ```

* **Ładuje dane przy użyciu bazy danych (przez wykonanie LoadDataToSQLDW. SQL) do usługi Azure Synapse Analytics** z konta prywatnego magazynu obiektów BLOB za pomocą następujących poleceń.

  * Utwórz schemat

    ```sql
    EXEC (''CREATE SCHEMA {schemaname};'');
    ```

  * Tworzenie poświadczeń w zakresie bazy danych

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
    WITH IDENTITY = ''asbkey'' ,
    Secret = ''{StorageAccountKey}''
    ```

  * Tworzenie zewnętrznego źródła danych dla obiektu BLOB usługi Azure Storage

    ```sql
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
    ```

  * Utwórz zewnętrzny format pliku CSV. Dane są nieskompresowane i pola są oddzielane znakiem potoku.

    ```sql
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
    ```

  * Utwórz zewnętrzną tabelę opłat i podróży dla zestawu danych NYC taksówki w usłudze Azure Blob Storage.

    ```sql
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
    ```

    - Ładowanie danych z tabel zewnętrznych w usłudze Azure Blob Storage do usługi Azure Synapse Analytics

      ```sql
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
      ```

    - Utwórz przykładową tabelę danych (NYCTaxi_Sample) i Wstaw do niej dane, wybierając pozycję zapytania SQL w tabelach podróży i opłat. (W niektórych krokach tego instruktażu należy użyć tej przykładowej tabeli).

      ```sql
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
      ```

Lokalizacja geograficzna kont magazynu ma wpływ na czasy ładowania.

> [!NOTE]
> W zależności od lokalizacji geograficznej konta prywatnego magazynu obiektów BLOB proces kopiowania danych z publicznego obiektu BLOB do prywatnego konta magazynu może zająć około 15 minut, a nawet dłużej, a proces ładowania danych z konta magazynu do usługi Azure Synapse Analytics może trwać 20 minut lub dłużej.
>
>

Należy zdecydować, co zrobić, jeśli masz zduplikowane pliki źródłowe i docelowe.

> [!NOTE]
> Jeśli pliki CSV, które mają zostać skopiowane z publicznego magazynu obiektów BLOB do prywatnego konta magazynu obiektów blob, już istnieją na koncie prywatnego magazynu obiektów blob, AzCopy wyświetli monit z pytaniem o to, czy chcesz je zastąpić. Jeśli nie chcesz ich zastąpić, wprowadź **n** po wyświetleniu monitu. Jeśli chcesz zastąpić **wszystkie** z nich **, wprowadź po** wyświetleniu monitu. Możesz również wprowadzić **y** , aby zastąpić pliki CSV pojedynczo.
>
>

![Wyjście z AzCopy][21]

Możesz użyć własnych danych. Jeśli Twoje dane są na komputerze lokalnym w swojej aplikacji w rzeczywistości, można nadal używać AzCopy do przekazywania danych lokalnych do prywatnego magazynu obiektów blob platformy Azure. Wystarczy zmienić lokalizację **źródłową** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"` w poleceniu AzCopy pliku skryptu programu PowerShell do katalogu lokalnego, który zawiera dane.

> [!TIP]
> Jeśli Twoje dane są już w prywatnym magazynie obiektów blob platformy Azure w aplikacji rzeczywistej, możesz pominąć krok AzCopy w skrypcie programu PowerShell i bezpośrednio przekazać dane do usługi Azure Synapse Analytics. Będzie to wymagało dodatkowych modyfikacji skryptu w celu dopasowania go do formatu danych.
>
>

Ten skrypt programu PowerShell umożliwia również podłączenie informacji analitycznych usługi Azure Synapse do plików przykładowych eksploracji danych SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb i SQLDW_Explorations_Scripts. PR, aby te trzy pliki były gotowe do natychmiastowego wypróbowania po zakończeniu działania skryptu programu PowerShell.

Po pomyślnym wykonaniu tego ekranu zobaczysz ekran podobny do poniższego:

![Dane wyjściowe pomyślnego wykonania skryptu][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Eksploracja danych i inżynieria funkcji w usłudze Azure Synapse Analytics
W tej sekcji przeprowadzamy eksplorowanie i generowanie funkcji, uruchamiając zapytania SQL bezpośrednio w usłudze Azure Synapse Analytics przy użyciu **narzędzi danych programu Visual Studio**. Wszystkie zapytania SQL użyte w tej sekcji znajdują się w przykładowym skrypcie o nazwie *SQLDW_Explorations. SQL*. Ten plik został już pobrany do katalogu lokalnego przez skrypt programu PowerShell. Można go również pobrać z witryny [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Jednak plik w usłudze GitHub nie ma podłączonych informacji o usłudze Azure Synapse Analytics.

Połącz się z usługą Azure Synapse Analytics przy użyciu programu Visual Studio za pomocą nazwy logowania i hasła usługi Azure Synapse Analytics, a następnie otwórz **Eksplorator obiektów SQL** w celu potwierdzenia, że baza danych i tabele zostały zaimportowane. Pobierz plik *SQLDW_Explorations. SQL* .

> [!NOTE]
> Aby otworzyć Edytor zapytań programu Parallel Data Warehouse (PDW), użyj polecenia **New Query** , gdy w **programie SQL Eksplorator obiektów**wybrano polecenie PDW. Standardowy Edytor zapytań SQL nie jest obsługiwany przez program PDW.
>
>

Poniżej przedstawiono typy zadań eksploracji danych i generowania funkcji wykonanych w tej sekcji:

* Eksplorowanie dystrybucji danych kilku pól w różnych oknach czasu.
* Zbadaj jakość danych pól długości i szerokości geograficznej.
* Generowanie etykiet klasyfikacji danych binarnych i wieloklasowych na podstawie ** \_ kwoty**pozostałej.
* Generuj funkcje i odległość wyjazdu obliczeniowego/porównania.
* Dołącz dwie tabele i Wyodrębnij losową próbkę, która będzie używana do kompilowania modeli.

### <a name="data-import-verification"></a>Weryfikacja importowania danych
Te zapytania zapewniają szybką weryfikację liczby wierszy i kolumn w tabelach wypełnionych wcześniej przy użyciu równoległego importu zbiorczego,

--Raportuj liczbę wierszy w tabeli <nyctaxi_trip> bez skanowania tabeli

   ```sql
   SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
   ```

--Raportuj liczbę kolumn w tabeli <nyctaxi_trip>

   ```sql
   SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'
   ```

**Dane wyjściowe:** Należy uzyskać 173 179 759 wierszy i 14 kolumn.

### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: dystrybucja podróży według Medallion
To przykładowe zapytanie identyfikuje Medallions (liczby taksówki), które ukończyły ponad 100 podróży w określonym przedziale czasu. Zapytanie jest korzystne z poziomu dostępu do partycjonowanej tabeli, ponieważ jest ono warunkiem schematu partycji typu ** \_ DateTime**. Wykonywanie zapytania dotyczącego pełnego zestawu danych spowoduje również użycie partycjonowanej tabeli i/lub skanowania indeksu.

```sql
SELECT medallion, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

**Dane wyjściowe:** Zapytanie powinno zwrócić tabelę z wierszami określającymi 13 369 Medallions (taksówki) i liczbę podróży ukończonych w 2013. Ostatnia kolumna zawiera liczbę zakończonych podróży.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Eksploracja: dystrybucja podróży według Medallion i hack_license
W tym przykładzie zidentyfikowano Medallions (numery taksówki) i numery hack_license (sterowniki), które ukończyły ponad 100 podróży w określonym przedziale czasu.

```sql
SELECT medallion, hack_license, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

**Dane wyjściowe:** Zapytanie powinno zwrócić tabelę z 13 369 wierszami 13 369 określającymi identyfikatory samochodów/sterowników, które ukończyły ponad 100 podróży w 2013. Ostatnia kolumna zawiera liczbę zakończonych podróży.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Ocena jakości danych: Weryfikuj rekordy z nieprawidłową długością geograficzną i/lub szerokością geograficzną
Ten przykład sprawdza, czy którekolwiek z pól długości geograficznej i/lub szerokości zawiera nieprawidłową wartość (w radianach stopni powinna należeć do zakresu od-90 do 90), czy ma (0, 0) współrzędnych.

```sql
SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

**Dane wyjściowe:** Zapytanie zwraca 837 467 TRIPS, które mają nieprawidłowe pola długości geograficznej i/lub szerokości geograficznej.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Eksploracja: rozchylony a przechylony
Ten przykład umożliwia znalezienie liczby przetworzonych podróży i liczby, które nie zostały wychylone w określonym przedziale czasu (lub w pełnym zestawie danych, jeśli obejmują cały rok, w jakim został on skonfigurowany). Ta dystrybucja odzwierciedla rozkład etykiet binarnych, który będzie później używany do modelowania klasyfikacji binarnej.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM <schemaname>.<nyctaxi_fare>
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

**Dane wyjściowe:** Zapytanie powinno zwrócić następujące częstotliwości Porada dla roku 2013:90 447 622, które zostały przechylone i 82 264 709 nie są wyrzucane.

### <a name="exploration-tip-classrange-distribution"></a>Eksploracja: rozmieszczenie klasy/zakresu etykiet
Ten przykład oblicza rozkład zakresów etykiet w danym okresie (lub w pełnym zestawie danych, jeśli obejmują cały rok). Ta dystrybucja klas etykiet zostanie później użyta w przypadku modelowania klasyfikacji wieloklasowej.

```sql
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
```

**Rozdzielczości**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Eksploracja: Obliczanie i porównywanie odległości podróży
Ten przykład konwertuje czas odbioru i Dropoff oraz szerokość geograficzną na punkty geograficzne SQL, oblicza odległość podróży przy użyciu różnic punktów geograficznych SQL i zwraca losową próbkę wyników do porównania. Przykład ogranicza wyniki do prawidłowych współrzędnych tylko przy użyciu zapytania oceny jakości danych pokrytego wcześniej.

```sql
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
```

### <a name="feature-engineering-using-sql-functions"></a>Inżynieria funkcji przy użyciu funkcji SQL
Czasami funkcje SQL mogą być wydajną opcją dla inżynierii funkcji. W tym instruktażu zdefiniowano funkcję SQL do obliczania bezpośredniej odległości między lokalizacjami odbioru i Dropoff. Poniższe skrypty SQL można uruchomić w **narzędziach danych programu Visual Studio**.

Oto skrypt SQL, który definiuje funkcję odległość.

```sql
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
```

Oto przykład, aby wywołać tę funkcję w celu wygenerowania funkcji w kwerendzie SQL:

--Przykładowe zapytanie do wywołania funkcji w celu utworzenia funkcji

   ```sql
SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
   ```

**Dane wyjściowe:** To zapytanie generuje tabelę (z 2 803 538 wierszami) z pobraniem i Dropoff Latitudes i długością geograficzną oraz odpowiednimi odległościami bezpośrednimi w milach. Poniżej przedstawiono wyniki dla pierwszych trzech wierszy:

| (Numer wiersza) | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40,731804 |-74,001083 |40,736622 |-73,988953 |.7169601222 |
| 2 |40,715794 |-74, 010635 |40,725338 |-74,00399 |.7448343721 |
| 3 |40,761456 |-73,999886 |40,766544 |-73,988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Przygotowywanie danych do kompilowania modelu
Następujące zapytanie sprzęga **nyctaxie \_ ** i **nyctaxi \_ taryfy** czasowe, generuje tablicę klasyfikacji **tipped**danych binarnych, ** \_ klasę**etykiet klasyfikacji wieloklasowego i wyodrębnia przykład z pełnego dołączonego zestawu danych. Próbkowanie jest wykonywane przez pobranie podzestawu TRIPS na podstawie czasu odbioru.  To zapytanie można skopiować, a następnie wkleić bezpośrednio w [Azure Machine Learning Studio (klasyczny)](https://studio.azureml.net) [Importuj dane]import[-Data] modułu do bezpośredniego pozyskiwania danych z wystąpienia SQL Database na platformie Azure. Zapytanie wyklucza rekordy z nieprawidłowymi współrzędnymi (0, 0).

```sql
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
```

Gdy wszystko będzie gotowe do przejścia do Azure Machine Learning, możesz wykonać następujące czynności:

1. Zapisz ostateczną kwerendę SQL w celu wyodrębnienia i próbkowania danych i skopiowania zapytania bezpośrednio do modułu[Import] danych w programie Azure Machine Learning lub
2. Utrwalaj próbkowane i zaprojektowane dane, które planujesz użyć do kompilowania modelu w nowej tabeli usługi Azure Synapse Analytics, i użyj nowej tabeli w module [Import danych][Import-Data] w Azure Machine Learning. Skrypt programu PowerShell w ramach wcześniejszego kroku zakończył to zadanie. Można odczytywać bezpośrednio z tej tabeli w module Importuj dane.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Eksploracja danych i inżynieria funkcji w notesie IPython
W tej sekcji będziemy wykonywać eksplorowanie i generowanie funkcji przy użyciu zapytań w języku Python i SQL względem utworzonej wcześniej usługi Azure Synapse Analytics. Przykładowy Notes IPython o nazwie **SQLDW_Explorations. ipynb** i plik skryptu w języku Python **SQLDW_Explorations_Scripts. PR** został pobrany do katalogu lokalnego. Są one również dostępne w serwisie [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Te dwa pliki są identyczne w skryptach języka Python. Plik skryptu języka Python jest udostępniany w przypadku braku serwera notesu IPython. Te dwa przykładowe pliki w języku Python zostały zaprojektowane w języku **python 2,7**.

Odpowiednie informacje o usłudze Azure Synapse Analytics w notesie przykładowym IPython i plik skryptu języka Python pobrane na komputer lokalny został wcześniej podłączony przez skrypt programu PowerShell. Są one wykonywalne bez żadnych modyfikacji.

Jeśli już skonfigurowano obszar roboczy Azure Machine Learning, możesz bezpośrednio przekazać przykładowy Notes IPython do usługi Azure IPython Notes i uruchomić go. Poniżej przedstawiono procedurę przekazywania do usługi Azure IPython Notes:

1. Zaloguj się do obszaru roboczego Azure Machine Learning, kliknij przycisk **Studio** u góry, a następnie kliknij pozycję **notesy** po lewej stronie strony sieci Web.

    ![Kliknij kolejno pozycje Studio i NOTESy][22]
2. Kliknij pozycję **Nowy** w lewym dolnym rogu strony sieci Web, a następnie wybierz pozycję **Python 2**. Następnie podaj nazwę notesu i kliknij znacznik wyboru, aby utworzyć nowy pusty Notes IPython.

    ![Kliknij pozycję Nowy, a następnie wybierz pozycję Python 2][23]
3. Kliknij symbol **Jupyter** w lewym górnym rogu nowego notesu IPython.

    ![Kliknij symbol Jupyter][24]
4. Przeciągnij i upuść przykładowy Notes IPython na stronę **drzewa** usługi Azure IPython Notes, a następnie kliknij pozycję **Przekaż**. Następnie przykładowy Notes IPython zostanie przekazany do usługi Azure IPython Notes.

    ![Kliknij pozycję Przekaż.][25]

W celu uruchomienia przykładowego notesu IPython lub pliku skryptu języka Python są konieczne następujące pakiety języka Python. Jeśli używasz usługi Azure IPython Notes, te pakiety zostały wstępnie zainstalowane.

- Pandas
- numpy
- matplotlib
- pyodbc
- PyTables

W przypadku kompilowania zaawansowanych rozwiązań analitycznych na Azure Machine Learning z dużymi danymi zalecaną sekwencją są:

* Zapoznaj się z małą próbką danych do ramki danych znajdującej się w pamięci.
* Wykonaj wizualizacje i eksploracje przy użyciu danych próbkowanych.
* Eksperymentuj z funkcjami inżynierii przy użyciu danych próbkowanych.
* W celu uzyskania większej eksploracji danych, manipulowania danymi i inżynierii funkcji Użyj języka Python, aby wystawiać zapytania SQL bezpośrednio z usługą Azure Synapse Analytics.
* Określ rozmiar próbki, który ma być odpowiedni dla kompilowania modelu Azure Machine Learning.

Poniżej przedstawiono kilka przykładów eksploracji danych, wizualizacji danych i inżynierów funkcji. Więcej eksploracji danych można znaleźć w notesie przykładowej IPython i przykładowym pliku skryptu w języku Python.

### <a name="initialize-database-credentials"></a>Zainicjuj poświadczenia bazy danych
Zainicjuj ustawienia połączenia z bazą danych w następujących zmiennych:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database driver>
```

### <a name="create-database-connection"></a>Utwórz połączenie z bazą danych
Oto parametry połączenia, które tworzą połączenie z bazą danych.

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Liczba raportów o liczbie wierszy i kolumn w tabeli <nyctaxi_trip>

```sql
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
```

* Łączna liczba wierszy = 173179759
* Łączna liczba kolumn = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Liczba raportów o liczbie wierszy i kolumn w tabeli <nyctaxi_fare>

```sql
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
```

* Łączna liczba wierszy = 173179759
* Łączna liczba kolumn = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Zapoznaj się z niewielkim przykładem danych z bazy danych usługi Azure Synapse Analytics

```sql
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
```

Czas odczytywania przykładowej tabeli wynosi 14,096495 sekund.
Liczba pobranych wierszy i kolumn = (1000, 21).

### <a name="descriptive-statistics"></a>Statystyki opisowe
Teraz możesz przystąpić do eksplorowania przykładowych danych. Zaczynamy od przejrzenia niektórych opisowych statystyk dotyczących ** \_ odległości podróży** (lub innych pól, które można określić).

```sql
df1['trip_distance'].describe()
```

### <a name="visualization-box-plot-example"></a>Wizualizacja: przykład wykresu skrzynkowego
Następnie Spójrzmy na wykres w polu odległość podróży, aby wizualizować quantiles.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Wyjście okna][1]

### <a name="visualization-distribution-plot-example"></a>Wizualizacja: przykład wykresu dystrybucji
Kreśla wizualizacje dystrybucji i histogramu dla przykładowych odległości podróży.

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Dane wyjściowe wykresu dystrybucji][2]

### <a name="visualization-bar-and-line-plots"></a>Wizualizacja: wykresy słupkowe i liniowe
W tym przykładzie przedziały czas podróży do pięciu pojemników i wizualizacji wyników pakowania.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Można wykreślić powyższą dystrybucję pojemników na pasku lub w wierszu z:

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Wykres słupkowy][3]

oraz

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```

![Wyjście liniowe][4]

### <a name="visualization-scatterplot-examples"></a>Wizualizacja: przykłady Scatterplot
Pokazujemy wykres punktowy **między \_ czasem podróży \_ w \_ sekundach** i ** \_ odległości podróży** , aby sprawdzić, czy istnieje korelacja

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Scatterplot dane wyjściowe relacji między czasem a odległością][6]

Podobnie możemy sprawdzić relację między ** \_ kodem częstotliwości** i ** \_ odległością podróży**.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Scatterplot dane wyjściowe relacji między kodem i odległością][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Eksploracja danych na przykładowych danych przy użyciu zapytań SQL w notesie IPython
W tej sekcji eksplorujemy dystrybucje danych przy użyciu danych próbkowanych, które są utrwalane w nowo utworzonej tabeli. Podobne eksploracje mogą być wykonywane przy użyciu oryginalnych tabel.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Eksploracja: Raportuj liczbę wierszy i kolumn w tabeli próbkowanej

```sql
nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
print 'Number of rows in sample = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
print 'Number of columns in sample = %d' % ncols.iloc[0,0]
```

#### <a name="exploration-tippednot-tripped-distribution"></a>Eksploracja: przerzucana/nieobsługiwana dystrybucja

```sql
query = '''
SELECT tipped, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tipped
    '''

    pd.read_sql(query, conn)
```

#### <a name="exploration-tip-class-distribution"></a>Eksploracja: dystrybucja klasy TIP

```sql
query = '''
    SELECT tip_class, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tip_class
'''

tip_class_dist = pd.read_sql(query, conn)
```

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Eksploracja: Wykreślanie rozkładu TIP według klasy

```sql
tip_class_dist['tip_freq'].plot(kind='bar')
```

![#26 wykresu][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Eksploracja: codzienne rozpowszechnianie podróży

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Eksploracja: rozkład podróży na Medallion

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Eksploracja: dystrybucja podróży według licencji Medallion i hakerów

```sql
query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-time-distribution"></a>Eksploracja: dystrybucja czasu podróży

```sql
query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distance-distribution"></a>Eksploracja: rozkład odległości podróży

```sql
query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-payment-type-distribution"></a>Eksploracja: dystrybucja typów płatności

```sql
query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
pd.read_sql(query,conn)
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Sprawdź ostateczną formę tabeli featurized

```sql
query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
pd.read_sql(query,conn)
```

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Tworzenie modeli w Azure Machine Learning
Teraz możemy przystąpić do tworzenia modeli i wdrażania modeli w [Azure Machine Learning](https://studio.azureml.net). Dane są gotowe do użycia w każdym z wymienionych wcześniej problemów przewidywania, mianowicie:

1. **Klasyfikacja binarna**: w celu przewidywania, czy Porada została zapłacona za podróż.
2. **Klasyfikacja wieloklasowa**: aby przewidzieć zakres płatnej porady zgodnie z wcześniej zdefiniowanymi klasami.
3. **Zadanie regresji**: przewidywanie kwoty Porada płatnej dla podróży.

Aby rozpocząć ćwiczenie modelowania, zaloguj się do obszaru roboczego **Azure Machine Learning (klasycznego)** . Jeśli nie utworzono jeszcze obszaru roboczego uczenia maszynowego, zobacz [Tworzenie obszaru roboczego Azure Machine Learning Studio (klasycznego)](../classic/create-workspace.md).

1. Aby rozpocząć pracę z Azure Machine Learning, zobacz [co to jest Azure Machine Learning Studio (klasyczny)?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Zaloguj się do [Azure Machine Learning Studio (klasyczne)](https://studio.azureml.net).
3. Strona główna Machine Learning Studio (klasyczna) zawiera wiele informacji, filmów wideo, samouczków, linków do odwołań do modułów i innych zasobów. Aby uzyskać więcej informacji na temat Azure Machine Learning, zobacz [centrum dokumentacji Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typowy eksperyment szkoleniowy składa się z następujących kroków:

1. Utwórz **Nowy** eksperyment.
2. Pobierz dane do Azure Machine Learning Studio (klasyczne).
3. Przed przetworzeniem i przekształcaniem danych oraz manipulowanie nimi.
4. Generuj funkcje zgodnie z wymaganiami.
5. Podziel dane na szkolenia/sprawdzanie poprawności/testowanie zestawów danych (lub Podziel zestawy danych dla każdego z nich).
6. Wybierz co najmniej jeden algorytm uczenia maszynowego w zależności od problemu szkoleniowego do rozwiązania. Na przykład klasyfikacja binarna, klasyfikacja wieloklasowa, regresja.
7. Uczenie jednego lub kilku modeli przy użyciu zestawu danych szkoleniowych.
8. Ocena zestawu danych walidacji przy użyciu przeszkolonych modeli.
9. Oceń modele, aby obliczyć odpowiednie metryki dla problemu szkoleniowego.
10. Dostosuj modele i wybierz najlepszy model do wdrożenia.

W tym ćwiczeniu zostały już omówione i zaprojektowane dane w usłudze Azure Synapse Analytics i podjęto decyzję o wielkości próbki do pozyskania w Azure Machine Learning Studio (klasyczny). Poniżej przedstawiono procedurę tworzenia jednego lub kilku modeli predykcyjnych:

1. Pobierz dane do Azure Machine Learning Studio (klasycznego) przy użyciu modułu [Import danych][Import-Data] , który jest dostępny w sekcji **dane wejściowe i wyjściowe** . Aby uzyskać więcej informacji, zapoznaj się z informacjami na stronie [Import Data][Import-Data] modułu.

    ![Importowanie danych z platformy Azure ML][17]
2. Wybierz **Azure SQL Database** jako **Źródło danych** w panelu **Właściwości** .
3. Wprowadź nazwę DNS bazy danych w polu **Nazwa serwera bazy danych** . Formatowanie `tcp:<your_virtual_machine_DNS_name>,1433`
4. Wprowadź **nazwę bazy danych** w odpowiednim polu.
5. Wprowadź *nazwę użytkownika SQL* w polu **nazwa konta użytkownika serwera**i *hasło* w polu **hasło konta użytkownika serwera**.
7. W obszarze tekst **kwerendy bazy danych** Edytuj zapytanie, które wyodrębnia niezbędne pola bazy danych (w tym wszystkie pola obliczane, takie jak etykiety), i w dół próbkuje dane do żądanego rozmiaru próbki.

Przykładem eksperymentu klasyfikacji binarnej odczytującego dane bezpośrednio z bazy danych usługi Azure Synapse Analytics znajduje się poniższy rysunek (Pamiętaj, aby zastąpić nazwy tabel nyctaxi_trip i nyctaxi_fare według nazwy schematu i nazw tabel użytych w przewodniku). Podobne eksperymenty mogą być zbudowane w przypadku problemów klasyfikacji i regresji wieloklasowej.

![Uczenie maszynowe platformy Azure][10]

> [!IMPORTANT]
> W zapytaniach dotyczących wyodrębniania i próbkowania danych modelowania, które zostały podane w poprzednich sekcjach, **wszystkie etykiety dla trzech ćwiczeń modelowania są zawarte w zapytaniu**. Ważne (wymagane) krok w każdym z ćwiczeń modelowania polega na **wykluczeniu** niepotrzebnych etykiet dla innych dwóch problemów oraz wszelkich innych **przecieków docelowych**. Na przykład, w przypadku używania klasyfikacji binarnej, użyj etykiety **przechylonej** i Wyklucz ** \_ klasę etykietki**pól, ** \_ kwotę wskazówki**i **łączną \_ kwotę**. Te ostatnie są wyciekami docelowymi, ponieważ implikują zapłacone wskazówki.
>
> Aby wykluczyć niepotrzebne kolumny lub wycieki docelowe, możesz użyć modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ) lub [edytować metadane][edit-metadata]. Aby uzyskać więcej informacji, zobacz [Wybieranie kolumn w zestawie danych][select-columns] i edytowanie stron odwołań do [metadanych][edit-metadata] .
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Wdrażanie modeli w Azure Machine Learning
Gdy model jest gotowy, możesz go łatwo wdrożyć jako usługę sieci Web bezpośrednio z eksperymentu. Aby uzyskać więcej informacji na temat wdrażania usług sieci Web Azure ML, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).

Aby wdrożyć nową usługę sieci Web, należy:

1. Utwórz eksperyment oceniania.
2. Wdróż usługę sieci Web.

Aby utworzyć eksperyment oceniania z **gotowego** eksperymentu szkoleniowego, kliknij pozycję **Utwórz eksperyment oceniania** na niższym pasku akcji.

![Ocenianie platformy Azure][18]

Azure Machine Learning podejmie próbę utworzenia eksperymentu oceniania na podstawie składników eksperymentu szkoleniowego. W szczególności będzie:

1. Zapisz przeszkolony model i Usuń moduły szkoleń modeli.
2. Określ logiczny **port wejściowy** reprezentujący oczekiwany schemat danych wejściowych.
3. Określ logiczny **port wyjściowy** reprezentujący oczekiwany schemat danych wyjściowych usługi sieci Web.

Podczas tworzenia eksperymentu oceniania Przejrzyj wyniki i dostosuj je w razie konieczności. Typowym ustawieniem jest zastępowanie wejściowego zestawu danych lub zapytania, które wyklucza pola etykiety, ponieważ te pola etykiet nie będą mapowane do schematu podczas wywoływania usługi. Dobrym sposobem jest zmniejszenie rozmiaru wejściowego zestawu danych i/lub zapytania do kilku rekordów, wystarczająco że wskazuje schemat wejściowy. Dla portu wyjściowego często wyklucza wszystkie pola wejściowe i zawiera tylko **etykiety z wynikami** i **oceny prawdopodobieństwa** w danych wyjściowych za pomocą modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

Przykładowy eksperyment oceniania znajduje się na poniższej ilustracji. Gdy wszystko będzie gotowe do wdrożenia, kliknij przycisk **Publikuj usługę sieci Web** na dolnym pasku akcji.

![Publikowanie w usłudze Azure ML][11]

## <a name="summary"></a>Podsumowanie
Aby podsumowanie, co zostało zrobione w tym samouczku instruktażu, utworzono środowisko nauki o danych platformy Azure, które działało z dużym publicznym zestawem danych, dzięki czemu można to zrobić za pośrednictwem procesu uczenia się o dane zespołowe, a następnie do wdrożenia usługi sieci Web Azure Machine Learning.

### <a name="license-information"></a>Informacje o licencji
Ten przykładowy przewodnik i towarzyszące mu skrypty i notesy IPython są udostępniane przez firmę Microsoft w ramach licencji MIT. Aby uzyskać więcej informacji, zapoznaj się z plikiem LICENSE.txt w katalogu przykładowego kodu w witrynie GitHub.

## <a name="references"></a>Odwołania
- [Strona pobierania podróży z Andrés Monroy NYC](https://www.andresmh.com/nyctaxitrips/)
- [Dane o podróży z taksówką NYC przez Krzysztof Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [Badania i dane statystyczne dotyczące NYC taksówki i Limousine Komisji](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
