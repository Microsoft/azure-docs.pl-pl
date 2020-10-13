---
title: Skalowalna nauka danych dzięki rozAzure Data Lakeemu procesowi analizy danych w zespole
description: Jak używać Azure Data Lake do eksploracji danych i binarnych zadań klasyfikacji w zestawie danych.
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
ms.openlocfilehash: b45cc87c525ab66a3807f71901728e60d086ea74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440409"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Skalowalna nauka danych dzięki Azure Data Lake: kompleksowy przewodnik
W tym instruktażu pokazano, jak używać Azure Data Lake do eksploracji danych i binarnych zadań klasyfikacji na przykład zestawu danych dotyczących podróży i taryfy z NYCą. Przeprowadzi Cię przez kroki [procesu analizy danych zespołu](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), kompleksowego, od pozyskiwania danych do szkoleń modelowych, a następnie do wdrożenia usługi sieci Web, która publikuje model.

## <a name="technologies"></a>Technologie

Te technologie są używane w tym instruktażu.
* Azure Data Lake Analytics
* U-SQL i Visual Studio
* Python
* Azure Machine Learning
* Skrypty


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Data Lake Microsoft Azure](https://azure.microsoft.com/solutions/data-lake/) ma wszystkie funkcje wymagane do ułatwienia analitykom danych przechowywanie danych dowolnego rozmiaru, kształtu i szybkości oraz do przetwarzania danych, zaawansowanej analizy i modelowania uczenia maszynowego z wysoką skalowalnością w opłacalny sposób.   Opłaty są naliczane według poszczególnych zadań, tylko wtedy, gdy dane są faktycznie przetwarzane. Azure Data Lake Analytics zawiera język U-SQL, który miesza deklaratywne cechy języka SQL z możliwością języka C#, aby zapewnić skalowalną funkcję zapytań rozproszonych. Dzięki temu można przetwarzać dane bez struktury, stosując schemat przy odczytywaniu, wstawiać niestandardowe logikę i funkcje zdefiniowane przez użytkownika (UDF), a także rozszerzalność umożliwiającą precyzyjne sterowanie sposobem ich wykonywania na dużą skalę. Aby dowiedzieć się więcej na temat zapoznania się z założeniami projektu w tle U-SQL, zobacz [wpis w blogu programu Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)

Data Lake Analytics jest również kluczową częścią pakietu Cortana Analytics i współpracuje z usługą Azure Synapse Analytics, Power BI i Data Factory. Ta kombinacja zapewnia kompletną platformę do obsługi danych Big Data i zaawansowaną analizę.

Ten Instruktaż rozpoczyna się od opisu sposobu instalowania wymagań wstępnych i zasobów wymaganych do ukończenia zadań związanych z przetwarzaniem danych. Następnie przedstawia kroki przetwarzania danych za pomocą języka U-SQL i zawiera opis sposobu używania języka Python i Hive z Azure Machine Learning Studio (klasyczny) do kompilowania i wdrażania modeli predykcyjnych.

### <a name="u-sql-and-visual-studio"></a>U-SQL i Visual Studio
W tym instruktażu zaleca się używanie programu Visual Studio do edytowania skryptów U-SQL w celu przetworzenia zestawu danych. Skrypty U-SQL są opisane tutaj i udostępniane w osobnym pliku. Proces ten obejmuje pozyskiwanie, eksplorowanie i próbkowanie danych. Pokazano w nim także, jak uruchomić zadanie skryptu U-SQL z Azure Portal. Tabele Hive są tworzone dla danych w skojarzonym klastrze usługi HDInsight w celu ułatwienia kompilowania i wdrażania modelu klasyfikacji binarnej w Azure Machine Learning Studio.

### <a name="python"></a>Python
Ten Instruktaż zawiera również sekcję przedstawiającą sposób kompilowania i wdrażania modelu predykcyjnego przy użyciu języka Python z Azure Machine Learning Studio. Udostępnia on Jupyter Notes ze skryptami języka Python dla kroków tego procesu. Notes zawiera kod dla niektórych dodatkowych etapów i modeli inżynierów funkcji, takich jak Klasyfikacja wieloklasowa i modelowanie regresji oprócz binarnego modelu klasyfikacji przedstawionego w tym miejscu. Zadanie regresji służy do przewidywania wielkości końcówki na podstawie innych funkcji etykietki.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (klasyczny) służy do kompilowania i wdrażania modeli predykcyjnych przy użyciu dwóch metod: najpierw ze skryptami języka Python, a następnie z tabelami programu Hive w klastrze usługi HDInsight (Hadoop).

### <a name="scripts"></a>Skrypty
W tym instruktażu opisano tylko podstawowe czynności. Możesz pobrać pełny **skrypt U-SQL** i **Jupyter Notebook** z usługi [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tych tematów należy wykonać następujące czynności:

* Subskrypcja platformy Azure. Jeśli jeszcze tego nie masz, zobacz artykuł [Pobieranie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Rekomendowane Visual Studio 2013 lub nowszy. Jeśli nie masz jeszcze zainstalowanej żadnej z tych wersji, możesz pobrać bezpłatną wersję społeczności z [programu Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Zamiast programu Visual Studio, można również przesłać zapytania Azure Data Lake za pomocą Azure Portal. Instrukcje można znaleźć w temacie jak to zrobić zarówno w programie Visual Studio, jak i w portalu w sekcji zatytułowanej **dane procesu przy użyciu języka U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Przygotuj środowisko analizy danych dla Azure Data Lake
Aby przygotować środowisko analizy danych dla tego przewodnika, Utwórz następujące zasoby:

* Azure Data Lake Storage (ADLS)
* Azure Data Lake Analytics (ADLA)
* Konto usługi Azure Blob Storage
* Konto Azure Machine Learning Studio (klasyczne)
* Azure Data Lake Tools for Visual Studio (zalecane)

Ta sekcja zawiera instrukcje dotyczące sposobu tworzenia każdego z tych zasobów. W przypadku wybrania opcji używania tabel programu Hive z Azure Machine Learning zamiast języka Python w celu utworzenia modelu należy również zainicjować obsługę administracyjną klastra usługi HDInsight (Hadoop). Ta alternatywna procedura opisana w sekcji opcja 2.


> [!NOTE]
> **Azure Data Lake Store** można utworzyć oddzielnie lub podczas tworzenia **Azure Data Lake Analytics** jako magazynu domyślnego. Do tworzenia poszczególnych zasobów są przywoływane instrukcje, ale konto magazynu Data Lake nie musi być tworzone osobno.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Tworzenie Azure Data Lake Storage


Utwórz element ADLS na podstawie [Azure Portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz [Tworzenie klastra usługi HDInsight z Data Lake Store przy użyciu Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Pamiętaj, aby skonfigurować tożsamość usługi AAD klastra w bloku **DataSource** bloku **konfiguracji opcjonalnej** .

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Utwórz konto Azure Data Lake Analytics
Utwórz konto usługi ADLA na podstawie [Azure Portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz [Samouczek: wprowadzenie do Azure Data Lake Analytics przy użyciu Azure Portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta usługi Azure Blob Storage
Utwórz konto usługi Azure Blob Storage na podstawie [Azure Portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz sekcję Tworzenie konta magazynu w temacie [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Konfigurowanie konta Azure Machine Learning Studio (klasycznego)
Utwórz konto/Azure Machine Learning Studio (klasyczne) ze strony [Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning/) . Kliknij przycisk **Rozpocznij teraz** , a następnie wybierz pozycję "bezpłatny obszar roboczy" lub "standardowy obszar roboczy". Teraz wszystko jest gotowe do tworzenia eksperymentów w programie Azure Machine Learning Studio.

### <a name="install-azure-data-lake-tools-recommended"></a>Zainstaluj narzędzia Azure Data Lake [zalecane]
Zainstaluj Azure Data Lake narzędzia dla używanej wersji programu Visual Studio z [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Po zakończeniu instalacji Otwórz program Visual Studio. Karta Data Lake powinna zostać wyświetlona w menu u góry. Twoje zasoby platformy Azure powinny pojawić się w lewym panelu po zalogowaniu się na koncie platformy Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Zestaw danych podróży z NYC taksówkami
Zestaw danych użyty w tym miejscu jest publicznie dostępnym elementem DataSet — [zestawienie podróży NYC taksówkami](https://www.andresmh.com/nyctaxitrips/). Dane dotyczące podróży z taksówką NYC obejmują około 20 GB skompresowanych plików CSV (~ 48 GB nieskompresowanych), nagrywanie ponad 173 000 000 pojedynczych podróży i opłat za każdą podróż. Każdy rekord rejsu zawiera lokalizację i czas odbioru oraz Dropoff, a także numer licencji () i Medallion (unikatowy identyfikator taksówki). Dane obejmują wszystkie podróże w roku 2013 i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

Wolumin CSV "trip_data" zawiera szczegóły dotyczące podróży, takie jak liczba pasażerów, punkty odbioru i Dropoff, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

Wolumin CSV "trip_fare" zawiera szczegółowe informacje o opłatach za każdą podróż, takie jak typ płatności, kwota opłaty, opłata dodatkowa i podatki, porady i opłaty, a także łączną kwotę płatną. Oto kilka przykładowych rekordów:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Unikatowy klucz do przyłączenia \_ danych podróży i \_ opłaty za podróż są złożone z następujących trzech pól: Medallion, licencja na hakera \_ i \_ Data i godzina pobrania. Dostęp do nieprzetworzonych plików CSV można uzyskać z poziomu obiektu BLOB usługi Azure Storage. Skrypt U-SQL dla tego sprzężenia znajduje się w sekcji dotyczącej [podróży i taryf za sprzężenie](#join) .

## <a name="process-data-with-u-sql"></a>Przetwarzanie danych za pomocą języka U-SQL
Zadania przetwarzania danych przedstawione w tej sekcji obejmują pozyskiwanie, sprawdzanie jakości, eksplorowanie i próbkowanie danych. Przedstawiono również sposób sprzęgania tabel podróży i opłat. W ostatniej sekcji przedstawiono zadanie uruchamiania skryptu U-SQL na podstawie Azure Portal. Poniżej przedstawiono linki do każdej podsekcji:

* [Pozyskiwanie danych: odczytywanie danych z publicznego obiektu BLOB](#ingest)
* [Sprawdzanie jakości danych](#quality)
* [Eksploracja danych](#explore)
* [Dołącz do tabel podróży i opłat](#join)
* [Próbkowanie danych](#sample)
* [Uruchamianie zadań U-SQL](#run)

Skrypty U-SQL są opisane tutaj i udostępniane w osobnym pliku. Możesz pobrać pełne **skrypty U-SQL** z usługi [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Aby wykonać polecenie U-SQL, Otwórz program Visual Studio, kliknij **plik--> New--> Project**, wybierz **projekt U-SQL**i Zapisz go w folderze.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> W witrynie Azure Portal można używać języka U-SQL zamiast programu Visual Studio. Możesz przejść do zasobu Azure Data Lake Analytics w portalu i przesłać zapytania bezpośrednio, jak pokazano na poniższej ilustracji:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Pozyskiwanie danych: odczytywanie danych z publicznego obiektu BLOB

Lokalizacja danych w obiekcie blob platformy Azure jest przywoływana jako ** \_ Nazwa wasb://Container \@ konta usługi BLOB \_ Storage \_ \_ name.blob.Core.Windows.NET/blob_name** i może zostać wyodrębniona przy użyciu **Extractors.Csv ()**. Zastąp własną nazwę kontenera i nazwę konta magazynu w następujących skryptach dla nazwy kontenera nazwa \_ \@ konta usługi BLOB \_ Storage \_ \_ w adresie wasb. Ponieważ nazwy plików są w tym samym formacie, można użyć pliku ** \_ Data \_ \{ \* \} . csv** w celu odczytania wszystkich 12 plików podróży.

```sql
///Read in Trip data
@trip0 =
    EXTRACT
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string
// This is reading 12 trip data from blob
FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
USING Extractors.Csv();
```

Ponieważ istnieją nagłówki w pierwszym wierszu, należy usunąć nagłówki i zmienić typy kolumn na odpowiednie. Przetworzone dane można zapisać do Azure Data Lake Storage przy użyciu **swebhdfs://data_lake_storage_name. azuredatalakestorage. NET/folder_name/file_name**_ lub do konta usługi Azure Blob Storage przy użyciu  **wasb://container_name \@ blob_storage_account_name. blob. Core. windows. NET/blob_name**.

```sql
// change data types
@trip =
    SELECT
    medallion,
    hack_license,
    vendor_id,
    rate_code,
    store_and_fwd_flag,
    DateTime.Parse(pickup_datetime) AS pickup_datetime,
    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
    Int32.Parse(passenger_count) AS passenger_count,
    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
    Double.Parse(trip_distance) AS trip_distance,
    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
FROM @trip0
WHERE medallion != "medallion";

////output data to ADL
OUTPUT @trip
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
USING Outputters.Csv();

////Output data to blob
OUTPUT @trip
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
USING Outputters.Csv();
```

Podobnie można je odczytać w zestawach danych o taryfie. Kliknij prawym przyciskiem myszy Azure Data Lake Storage, możesz zdecydować się na dane w **Azure Portal > Eksplorator danych** lub **Eksploratorze plików** w programie Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Sprawdzanie jakości danych
Gdy tabele podróży i taryfy są odczytywane w programie, sprawdzanie jakości danych można wykonać w następujący sposób. Wynikowe pliki CSV mogą być wyprowadzane do usługi Azure Blob Storage lub Azure Data Lake Storage.

Znajdź liczbę Medallions i unikatową liczbę Medallions:

```sql
///check the number of medallions and unique number of medallions
@trip2 =
    SELECT
    medallion,
    vendor_id,
    pickup_datetime.Month AS pickup_month
    FROM @trip;

@ex_1 =
    SELECT
    pickup_month,
    COUNT(medallion) AS cnt_medallion,
    COUNT(DISTINCT(medallion)) AS unique_medallion
    FROM @trip2
    GROUP BY pickup_month;
    OUTPUT @ex_1
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
USING Outputters.Csv();
```

Znajdź te Medallions z ponad 100 podróży:

```sql
///find those medallions that had more than 100 trips
@ex_2 =
    SELECT medallion,
           COUNT(medallion) AS cnt_medallion
    FROM @trip2
    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
    GROUP BY medallion
    HAVING COUNT(medallion) > 100;
    OUTPUT @ex_2
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
USING Outputters.Csv();
```

Znajdź te nieprawidłowe rekordy pod warunkiem pickup_longitude:

```sql
///find those invalid records in terms of pickup_longitude
@ex_3 =
    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
    FROM @trip
    WHERE
    pickup_longitude <- 90 OR pickup_longitude > 90;
    OUTPUT @ex_3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
USING Outputters.Csv();
```

Znajdź brakujące wartości dla niektórych zmiennych:

```sql
//check missing values
@res =
    SELECT *,
           (medallion == null? 1 : 0) AS missing_medallion
    FROM @trip;

@trip_summary6 =
    SELECT
        vendor_id,
    SUM(missing_medallion) AS medallion_empty,
    COUNT(medallion) AS medallion_total,
    COUNT(DISTINCT(medallion)) AS medallion_total_unique
    FROM @res
    GROUP BY vendor_id;
OUTPUT @trip_summary6
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
USING Outputters.Csv();
```

### <a name="data-exploration"></a><a name="explore"></a>Eksploracja danych
Aby lepiej zrozumieć dane, należy przeprowadzić eksplorację danych przy użyciu następujących skryptów.

Znajdź dystrybucję przerzucanych i przerzucanych podróży:

```sql
///tipped vs. not tipped distribution
@tip_or_not =
    SELECT *,
           (tip_amount > 0 ? 1: 0) AS tipped
    FROM @fare;

@ex_4 =
    SELECT tipped,
           COUNT(*) AS tip_freq
    FROM @tip_or_not
    GROUP BY tipped;
    OUTPUT @ex_4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
USING Outputters.Csv();
```

Znajdź rozkład kwoty Tip z wartościami wyciętymi: 0, 5, 10 i 20 dolarów.

```sql
//tip class/range distribution
@tip_class =
    SELECT *,
           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @fare;
@ex_5 =
    SELECT tip_class,
           COUNT(*) AS tip_freq
    FROM @tip_class
    GROUP BY tip_class;
    OUTPUT @ex_5
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
USING Outputters.Csv();
```

Znajdź podstawowe statystyki odległości podróży:

```sql
// find basic statistics for trip_distance
@trip_summary4 =
    SELECT
        vendor_id,
        COUNT(*) AS cnt_row,
        MIN(trip_distance) AS min_trip_distance,
        MAX(trip_distance) AS max_trip_distance,
        AVG(trip_distance) AS avg_trip_distance
    FROM @trip
    GROUP BY vendor_id;
OUTPUT @trip_summary4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
USING Outputters.Csv();
```

Znajdź percentyle odległości podróży:

```sql
// find percentiles of trip_distance
@trip_summary3 =
    SELECT DISTINCT vendor_id AS vendor,
                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
    FROM @trip;
   // group by vendor_id;
OUTPUT @trip_summary3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
USING Outputters.Csv();
```

### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Dołącz do tabel podróży i opłat
Tabele podróży i opłat mogą być sprzężone przez Medallion, hack_license i pickup_time.

```sql
//join trip and fare table

@model_data_full =
SELECT t.*,
f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
(f.tip_amount > 0 ? 1: 0) AS tipped,
(f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
FROM @trip AS t JOIN  @fare AS f
ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

//// output to blob
OUTPUT @model_data_full
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
USING Outputters.Csv();

////output data to ADL
OUTPUT @model_data_full
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
USING Outputters.Csv();
```

Dla każdego poziomu liczby pasażerów Oblicz liczbę rekordów, średnią kwotę Porada, wariancję wartości pozostałej, procent przerzucanych podróży.

```sql
// contingency table
@trip_summary8 =
    SELECT passenger_count,
           COUNT(*) AS cnt,
           AVG(tip_amount) AS avg_tip_amount,
           VAR(tip_amount) AS var_tip_amount,
           SUM(tipped) AS cnt_tipped,
           (float)SUM(tipped)/COUNT(*) AS pct_tipped
    FROM @model_data_full
    GROUP BY passenger_count;
    OUTPUT @trip_summary8
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
USING Outputters.Csv();
```

### <a name="data-sampling"></a><a name="sample"></a>Próbkowanie danych
Najpierw wybierz losowo 0,1% danych z sprzężonej tabeli:

```sql
//random select 1/1000 data for modeling purpose
@addrownumberres_randomsample =
SELECT *,
        ROW_NUMBER() OVER() AS rownum
FROM @model_data_full;

@model_data_random_sample_1_1000 =
SELECT *
FROM @addrownumberres_randomsample
WHERE rownum % 1000 == 0;

OUTPUT @model_data_random_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
USING Outputters.Csv();
```

Następnie wykonaj próbkowanie stratified przez zmienną binarną tip_class:

```sql
//stratified random select 1/1000 data for modeling purpose
@addrownumberres_stratifiedsample =
SELECT *,
        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
FROM @model_data_full;

@model_data_stratified_sample_1_1000 =
SELECT *
FROM @addrownumberres_stratifiedsample
WHERE rownum % 1000 == 0;
//// output to blob
OUTPUT @model_data_stratified_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
////output data to ADL
OUTPUT @model_data_stratified_sample_1_1000
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
```

### <a name="run-u-sql-jobs"></a><a name="run"></a>Uruchamianie zadań U-SQL
Po edycji skryptów U-SQL można przesłać je na serwer przy użyciu konta Azure Data Lake Analytics. Kliknij **Data Lake**, **Prześlij zadanie**, wybierz **konto analizy**, wybierz **równoległość**, a następnie kliknij przycisk **Prześlij** .

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Po pomyślnym spełnieniu zadania stan zadania jest wyświetlany w programie Visual Studio do monitorowania. Po zakończeniu zadania można nawet powtórzyć proces wykonywania zadania i dowiedzieć się, jak zwiększyć wydajność zadania. Możesz również przejść do Azure Portal, aby sprawdzić stan zadań U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Teraz można sprawdzić pliki wyjściowe w usłudze Azure Blob Storage lub Azure Portal. Użyj przykładowych danych stratified do modelowania w następnym kroku.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Kompilowanie i wdrażanie modeli w Azure Machine Learning
Dostępne są dwie opcje pobierania danych do Azure Machine Learning do kompilowania i

* W pierwszej opcji należy użyć danych próbkowanych, które zostały zapisaną w obiekcie blob platformy Azure (w powyższym kroku **próbkowania danych** ) i użyć języka Python do kompilowania i wdrażania modeli z Azure Machine Learning.
* W drugiej opcji można wykonywać zapytania dotyczące danych w Azure Data Lake bezpośrednio przy użyciu zapytania programu Hive. Ta opcja wymaga utworzenia nowego klastra usługi HDInsight lub użycia istniejącego klastra usługi HDInsight, w którym tabele programu Hive wskazują dane dotyczące NY taksówki w Azure Data Lake Storage.  Obie te opcje zostały omówione w poniższych sekcjach.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opcja 1. Używanie języka Python do kompilowania i wdrażania modeli uczenia maszynowego
Aby skompilować i wdrożyć modele uczenia maszynowego przy użyciu języka Python, Utwórz Jupyter Notebook na komputerze lokalnym lub w Azure Machine Learning Studio. Jupyter Notebook udostępnione w witrynie [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) zawiera pełny kod umożliwiający Eksplorowanie, wizualizowanie danych, inżynierowanie cech, modelowanie i wdrażanie. W tym artykule opisano sposób modelowania i wdrażania.

### <a name="import-python-libraries"></a>Importowanie bibliotek języka Python
Aby można było uruchomić przykładowe Jupyter Notebook lub plik skryptu języka Python, są konieczne następujące pakiety języka Python. Jeśli używasz usługi notesu Azure Machine Learning, te pakiety zostały wstępnie zainstalowane.

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
import sklearn
from sklearn.linear_model import LogisticRegression
from sklearn.cross_validation import train_test_split
from sklearn import metrics
from __future__ import division
from sklearn import linear_model
from azureml import services
```

### <a name="read-in-the-data-from-blob"></a>Odczytaj dane z obiektu BLOB
* Parametry połączenia

  ```text
  CONTAINERNAME = 'test1'
  STORAGEACCOUNTNAME = 'XXXXXXXXX'
  STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
  BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
  blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
  ```

* Odczytaj jako tekst

  ```text
  t1 = time.time()
  data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
  t2 = time.time()
  print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  ```

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)

* Dodawanie nazw kolumn i oddzielnych kolumn

  ```text
  colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
  'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
  df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
  ```

* Zmień niektóre kolumny na liczbowe

  ```text
  cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
  ,'tipped','tip_class','rownum']
  for col in cols_2_float:
      df1[col] = df1[col].astype(float)
  ```

### <a name="build-machine-learning-models"></a>Kompiluj modele uczenia maszynowego
W tym miejscu utworzysz binarny model klasyfikacji w celu przewidywania, czy podróż jest wyrzucana. W Jupyter Notebook można znaleźć inne dwa modele: klasyfikacje wieloklasowe i modele regresji.

* Najpierw należy utworzyć zmienne fikcyjne, które mogą być używane w modelach scikit — nauka

  ```python
  df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
  df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
  ```

* Tworzenie ramki danych dla modelowania

  ```python
  cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
  data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

  X = data.iloc[:,1:]
  Y = data.tipped
    ```

* Szkolenia i testy 60-40 podzielone

  ```python
  X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
  ```

* Regresja logistyczna w zestawie szkoleń

  ```python
  model = LogisticRegression()
  logit_fit = model.fit(X_train, Y_train)
  print ('Coefficients: \n', logit_fit.coef_)
  Y_train_pred = logit_fit.predict(X_train)
  ```

    ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)

* Zestaw danych testowych oceny

  ```python
  Y_test_pred = logit_fit.predict(X_test)
  ```

* Oblicz metryki oceny

  ```python
  fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
  print fpr_train, tpr_train, thresholds_train

  fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
  print fpr_test, tpr_test, thresholds_test

  #AUC
  print metrics.auc(fpr_train,tpr_train)
  print metrics.auc(fpr_test,tpr_test)

  #Confusion Matrix
  print metrics.confusion_matrix(Y_train,Y_train_pred)
  print metrics.confusion_matrix(Y_test,Y_test_pred)
  ```

    ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Tworzenie interfejsu API usługi sieci Web i korzystanie z niego w języku Python
Chcesz operacjonalizować model uczenia maszynowego po jego skompilowaniu. Binarny model logistyczny jest używany tutaj jako przykład. Upewnij się, że wersja scikit-Dowiedz się na komputerze lokalnym, jest 0.15.1 (Azure Machine Learning Studio jest już co najmniej w tej wersji).

* Znajdź poświadczenia obszaru roboczego z ustawień Azure Machine Learning Studio (klasycznych). W Azure Machine Learning Studio kliknij pozycję **Ustawienia**  -->  **Name**  -->  **tokeny autoryzacji**.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

  ```output
  workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  ```

* Utwórz usługę sieci Web

  ```python
  @services.publish(workspaceid, auth_token)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(int) #0, or 1
  def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
      return logit_fit.predict(inputArray)
  ```

* Pobierz poświadczenia usługi sieci Web

  ```python
  url = predictNYCTAXI.service.url
  api_key =  predictNYCTAXI.service.api_key

  print url
  print api_key

  @services.service(url, api_key)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(float)
  def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      pass
  ```

* Wywoływanie interfejsu API usługi sieci Web. Zazwyczaj poczekaj 5-10 sekund po poprzednim kroku.

  ```python
  NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  ```

    ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opcja 2. Tworzenie i wdrażanie modeli bezpośrednio w Azure Machine Learning
Azure Machine Learning Studio (klasyczny) może odczytywać dane bezpośrednio z Azure Data Lake Storage, a następnie służyć do tworzenia i wdrażania modeli. To podejście używa tabeli programu Hive, która wskazuje na Azure Data Lake Storage. Dla tabeli programu Hive musi być zainicjowany oddzielny klaster usługi Azure HDInsight. 

### <a name="create-an-hdinsight-linux-cluster"></a>Tworzenie klastra usługi HDInsight w systemie Linux
Utwórz klaster usługi HDInsight (Linux) na podstawie [Azure Portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz sekcję **Tworzenie klastra usługi HDInsight z dostępem do Azure Data Lake Storage** w temacie [Tworzenie klastra usługi HDInsight z Data Lake Store przy użyciu Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Tworzenie tabeli programu Hive w usłudze HDInsight
Teraz utworzysz tabele Hive, które będą używane w Azure Machine Learning Studio (klasyczne) w klastrze usługi HDInsight przy użyciu danych przechowywanych w Azure Data Lake Storage w poprzednim kroku. Przejdź do utworzonego klastra usługi HDInsight. Kliknij kolejno pozycje **Ustawienia**  -->  **Właściwości**  -->  **klaster AAD Identity**  -->  **ADLS Access**, upewnij się, że konto Azure Data Lake Storage zostało dodane na liście z prawami odczyt, zapis i wykonywanie.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Następnie kliknij pozycję **pulpit nawigacyjny** obok przycisku **Ustawienia** , a okno pojawia się. Kliknij pozycję **widok programu Hive** w prawym górnym rogu strony i powinien zostać wyświetlony **Edytor zapytań**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Wklej w poniższym skrypcie Hive, aby utworzyć tabelę. Lokalizacja źródła danych znajduje się w odniesieniu do Azure Data Lake Storage w następujący sposób: **ADL://data_lake_store_name. azuredatalakestore. NET: 443/folder_name/file_name**.

```hiveql
CREATE EXTERNAL TABLE nyc_stratified_sample
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string,
  payment_type string,
  fare_amount string,
  surcharge string,
  mta_tax string,
  tolls_amount string,
  total_amount string,
  tip_amount string,
  tipped string,
  tip_class string,
  rownum string
  )
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';
```

Po zakończeniu zapytania powinny być widoczne następujące wyniki:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Kompilowanie i wdrażanie modeli w Azure Machine Learning Studio
Teraz można przystąpić do kompilowania i wdrażania modelu, który przewiduje, czy Porada jest płatna Azure Machine Learning. Dane przykładowe stratified są gotowe do użycia w tej klasyfikacji binarnej (etykietka lub nie). Modele predykcyjne wykorzystujące klasyfikacje wieloklasowe (tip_class) i regresję (tip_amount) mogą być również kompilowane i wdrażane za pomocą Azure Machine Learning Studio, ale w tym miejscu pokazano tylko, jak obsłużyć przypadek przy użyciu binarnego modelu klasyfikacji.

1. Pobierz dane do Azure Machine Learning Studio (klasyczne) przy użyciu modułu **Importuj dane** dostępnego w sekcji **dane wejściowe i wyjściowe** . Aby uzyskać więcej informacji, zobacz stronę odwołania [modułu importowania danych](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) .
2. Wybierz pozycję **zapytanie programu Hive** jako **Źródło danych** w panelu **Właściwości** .
3. Wklej następujący skrypt Hive w edytorze **zapytań bazy danych programu Hive**

    ```hiveql
    select * from nyc_stratified_sample;
    ```

4. Wprowadź identyfikator URI klastra usługi HDInsight (ten identyfikator URI można znaleźć w Azure Portal), poświadczenia usługi Hadoop, lokalizację danych wyjściowych i nazwę konta/klucz/nazwę kontenera usługi Azure Storage.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Przykładem eksperymentu klasyfikacji binarnej odczytywanie danych z tabeli programu Hive jest pokazane na poniższym rysunku:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Po utworzeniu eksperymentu kliknij pozycję Skonfiguruj usługę sieci **Web**  -->  **predykcyjną usługę sieci** Web

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Uruchom automatycznie utworzony eksperyment oceniania, po zakończeniu kliknij przycisk **Wdróż usługę sieci Web** .

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Pulpit nawigacyjny usługi sieci Web wyświetla się wkrótce:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Podsumowanie
Wykonanie tego przewodnika spowodowało utworzenie środowiska nauki o danych do tworzenia skalowalnych kompleksowych rozwiązań w Azure Data Lake. To środowisko zostało użyte do analizy dużego publicznego zestawu danych, przechodzenia przez kolejne etapy procesu analizy danych, od pozyskiwania danych za pośrednictwem szkoleń modelowych, a następnie do wdrożenia modelu jako usługi sieci Web. Skrypt U-SQL został użyty do przetworzenia, eksplorowania i próbkowania danych. Narzędzia Python i Hive zostały użyte z Azure Machine Learning Studio (klasyczny) do kompilowania i wdrażania modeli predykcyjnych.

## <a name="whats-next"></a>Co dalej?
Ścieżka szkoleniowa dla [procesu nauka danych zespołu (przetwarzania TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) zawiera linki do tematów opisujących każdy krok w procesie zaawansowanej analizy. Na stronie [przewodniki dotyczące procesów naukowych](walkthroughs.md) dotyczącej analizy danych zespołu znajduje się szereg instruktaży, które pokazują, jak używać zasobów i usług w różnych scenariuszach analiz predykcyjnych:

* [Proces nauki danych zespołu w działaniu: korzystanie z usługi Azure Synapse Analytics](sqldw-walkthrough.md)
* [Proces nauki danych zespołu w działaniu: korzystanie z klastrów usługi HDInsight Hadoop](hive-walkthrough.md)
* [Proces nauki danych zespołu: używanie SQL Server](sql-walkthrough.md)
* [Przegląd procesu nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md)
