---
title: Eksploruj dane w klastrze Hadoop — proces nauki o danych zespołowych
description: Korzystanie z procesu nauki o danych zespołu dla scenariusza end-to-end, przy użyciu klastra HDInsight Hadoop do tworzenia i wdrażania modelu.
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
ms.openlocfilehash: bf69786f56f52874bd9358ae44a6b88b466e77f4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677457"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Proces nauki o danych zespołu w akcji: użyj klastrów Usługi Azure HDInsight Hadoop
W tym instruktażu używamy [procesu nauki o danych zespołu (TDSP)](overview.md) w scenariuszu end-to-end. Używamy [klastra Usługi Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorowania i projektowania funkcji danych z publicznie dostępnego zestawu danych [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) i pobierania próbek danych w dół. Aby obsłużyć klasyfikację binarną i wieloklasową i zadania predykcyjne regresji, tworzymy modele danych za pomocą usługi Azure Machine Learning. 

Aby uzyskać przewodnik, który pokazuje, jak obsługiwać większy zestaw danych, zobacz [Proces nauki o danych zespołu — przy użyciu klastrów usługi Azure HDInsight Hadoop w zestawie danych o pojemności 1 TB.](hive-criteo-walkthrough.md)

Notesu IPython można również użyć do wykonania zadań przedstawionych w instruktażu, który używa zestawu danych o pojemności 1 TB. Aby uzyskać więcej informacji, zobacz [przewodnik Criteo przy użyciu połączenia HIVE ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Opis zestawu danych NYC Taxi Trips
Dane NYC Taxi Trip to około 20 GB skompresowanych plików wartości oddzielonych przecinkami (CSV) (~48 GB nieskompresowanych). Ma ponad 173 miliony indywidualnych podróży i obejmuje taryfy zapłacone za każdą podróż. Każdy rekord podróży zawiera miejsce odbioru i nadsyłania, anonimowy numer licencji hack (kierowcy) i numer medalionu (unikalny identyfikator taksówki). Dane obejmują wszystkie podróże w roku 2013 i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

- Pliki CSV trip_data zawierają szczegóły podróży: liczbę pasażerów, punkty odbioru i wysiadania, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Trip_fare pliki CSV zawierają szczegółowe informacje o taryfie zapłaconej za każdą podróż: rodzaj płatności, wysokość taryfy, dopłaty i podatki, wskazówki i opłaty drogowe oraz całkowitą zapłaconą kwotę. Oto kilka przykładowych rekordów:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unikalny klucz do\_przyłączenia\_się do danych podróży i taryfy podróży\_składa się\_z pól: medalion, licencja hack i data odbioru. Aby uzyskać wszystkie szczegóły istotne dla danej podróży, wystarczy dołączyć do tych trzech kluczy.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Przykłady zadań przewidywania
Określ rodzaj prognoz, które chcesz wprowadzić na podstawie analizy danych, aby ułatwić wyjaśnienie wymaganych zadań procesu. Oto trzy przykłady problemów z przewidywaniem, które rozwiązujemy w tym instruktażu, wszystkie oparte na *kwocie\_końcówki:*

- **Klasyfikacja binarna:** Wytypuj, czy napiwek został opłacony za podróż. Oznacza to, że *kwota napiwku,\_* która jest większa niż $0 jest pozytywnym przykładem, podczas gdy kwota *napiwku\_* $0 jest przykładem negatywnym.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Klasyfikacja wieloklasowa**: Wytypuj zakres kwot napiwków zapłaconych za podróż. Dzielimy *ilość\_końcówki* na pięć klas:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Zadanie regresji:** Wytypuj kwotę napiwku opłaconego za podróż.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Konfigurowanie klastra hdinsight Hadoop do zaawansowanej analizy
> [!NOTE]
> Zazwyczaj jest to zadanie administratora.
> 
> 

Można skonfigurować środowisko platformy Azure dla zaawansowanej analizy, która wykorzystuje klaster HDInsight w trzech krokach:

1. [Utwórz konto magazynu:](../../storage/common/storage-account-create.md)To konto magazynu służy do przechowywania danych w magazynie obiektów Blob platformy Azure. Dane używane w klastrach HDInsight również znajdują się tutaj.
2. [Dostosuj klastry usługi Azure HDInsight Hadoop do zaawansowanego procesu analitycznego i technologii](customize-hadoop-cluster.md). Ten krok tworzy klaster HDInsight Hadoop z 64-bitową anakondą Python 2.7 zainstalowaną na wszystkich węzłach. Podczas dostosowywania klastra HDInsight należy pamiętać o dwóch ważnych krokach.
   
   * Pamiętaj, aby połączyć konto magazynu utworzone w kroku 1 z klastrem HDInsight podczas jego tworzenia. To konto magazynu uzyskuje dostęp do danych, które są przetwarzane w klastrze.
   * Po utworzeniu klastra włącz dostęp zdalny do węzła głównego klastra. Przejdź do karty **Konfiguracja** i wybierz pozycję **Włącz zdalny**. Ten krok określa poświadczenia użytkownika używane do zdalnego logowania.
3. [Tworzenie obszaru roboczego usługi Azure Machine Learning:](../studio/create-workspace.md)Ten obszar roboczy służy do tworzenia modeli uczenia maszynowego. To zadanie zostało rozwiązane po zakończeniu wstępnej eksploracji danych i próbkowaniu w dół przy użyciu klastra HDInsight.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Pobierz dane ze źródła publicznego
> [!NOTE]
> Zazwyczaj jest to zadanie administratora.
> 
> 

Aby skopiować zestaw danych [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) na komputer z jego lokalizacji publicznej, należy użyć dowolnej z metod opisanych w obszarze [Przenoszenie danych do i z magazynu obiektów Blob platformy Azure.](move-azure-blob.md)

W tym miejscu opisujemy, jak używać AzCopy do przesyłania plików zawierających dane. Aby pobrać i zainstalować program AzCopy, postępuj zgodnie z instrukcjami dotyczącymi [narzędzia wiersza polecenia AzCopy](../../storage/common/storage-use-azcopy.md).

1. W oknie wiersza polecenia uruchom następujące polecenia AzCopy, zastępując * \<path_to_data_folder>* żądanym miejscem docelowym:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Po zakończeniu kopiowania w wybranym folderze danych pojawią się łącznie 24 spakowane pliki. Rozpaj pobrane pliki do tego samego katalogu na komputerze lokalnym. Zanotuj folder, w którym znajdują się nieskompresowane pliki. Ten folder jest określany jako * \<ścieżka\_\_do\> \_unzipped_data plików* w tym, co następuje.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Przekazywanie danych do domyślnego kontenera klastra HDInsight Hadoop
> [!NOTE]
> Zazwyczaj jest to zadanie administratora.
> 
> 

W poniższych poleceniach AzCopy zastąp następujące parametry rzeczywistymi wartościami określonymi podczas tworzenia klastra Hadoop i rozpakowywania plików danych.

* *** \<path_to_data_folder>*** Katalog (wraz ze ścieżką) na komputerze, który zawiera rozpakowane pliki danych.  
* ***nazwa konta magazynu>klastra Hadoop \<*** Konto magazynu skojarzone z klastrem HDInsight.
* ***domyślny kontener>klastra Hadoop \<*** Domyślny kontener używany przez klaster. Nazwa kontenera domyślnego jest zwykle taka sama jak sam klaster. Na przykład jeśli klaster jest nazywany "abc123.azurehdinsight.net", domyślnym kontenerem jest abc123.
* ***>klucza konta magazynu \<*** Klucz dla konta magazynu używanego przez klaster.

W wierszu polecenia lub w oknie programu Windows PowerShell uruchom następujące dwa polecenia AzCopy.

To polecenie przesyła dane podróży do katalogu ***nyctaxitripraw*** w domyślnym kontenerze klastra Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

To polecenie przekazuje dane taryfy do katalogu ***nyctaxifareraw*** w domyślnym kontenerze klastra Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Dane powinny być teraz w magazynie obiektów Blob i gotowe do użycia w klastrze HDInsight.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Zaloguj się do węzła głównego klastra Hadoop i przygotuj się do analizy danych odkrywczych
> [!NOTE]
> Zazwyczaj jest to zadanie administratora.
> 
> 

Aby uzyskać dostęp do węzła głównego klastra do analizy danych odkrywczych i pobierania próbek w dół danych, postępuj zgodnie z procedurą opisaną w [programie Access the head node of Hadoop Cluster](customize-hadoop-cluster.md).

W tym instruktażu używamy przede wszystkim kwerend napisanych w [hive](https://hive.apache.org/), języka zapytań podobne do SQL, do wykonywania wstępnych eksploracji danych. Zapytania hive są przechowywane w plikach '.hql'. Następnie możemy pobrać próbkę tych danych, które mają być używane w ramach uczenia maszynowego do tworzenia modeli.

Aby przygotować klaster do analizy danych odkrywczych, pobierz pliki ".hql" zawierające odpowiednie skrypty hive z [gitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) do katalogu lokalnego (C:\temp) w węźle głównym. Otwórz wiersz polecenia z węzła głównego klastra i uruchom następujące dwa polecenia:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Te dwa polecenia pobierają wszystkie pliki '.hql' potrzebne w tym instruktażu do katalogu lokalnego ***C:\temp&#92;*** w węźle głównym.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Tworzenie bazy danych i tabel gałęzi podzielonych na partycje według miesięcy
> [!NOTE]
> To zadanie jest zazwyczaj dla administratora.
> 
> 

Teraz można przystąpić do tworzenia tabel hive dla zestawu danych taksówki NYC.
W węźle głównym klastra Hadoop otwórz wiersz polecenia Hadoop na pulpicie węzła głównego. Wprowadź katalog Hive, uruchamiając następujące polecenie:

    cd %hive_home%\bin

> [!NOTE]
> Uruchom wszystkie polecenia hive w tym instruktażu z wiersza wiersza wiązki/katalogu. Automatycznie obsługuje wszelkie problemy ze ścieżką. Używamy terminów "Wiersz katalogu hive", "Hive bin/ directory prompt" i "Hadoop command line" zamiennie w tym instruktażu.
> 
> 

W wierszu katalogu Hive uruchom następujące polecenie w wierszu polecenia Hadoop węzła głównego, które tworzy bazę danych i tabele hive:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Oto zawartość **\_C:\temp\sample hive\_utworzyć\_plik\_\_db i tables.hql,** który tworzy bazy danych hive **nyctaxidb**, a tabele **podróży** i **taryfy**.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Ten skrypt gałęzi tworzy dwie tabele:

* Tabela **podróży** zawiera szczegóły podróży każdej jazdy (dane kierowcy, czas odbioru, odległość i godziny podróży).
* Tabela **taryf** zawiera szczegóły taryfy (kwota taryfy, kwota napiwku, opłaty drogowe i dopłaty).

Jeśli potrzebujesz dodatkowej pomocy w tych procedurach lub chcesz zbadać alternatywne, zobacz sekcję [Prześlij zapytania hive bezpośrednio z wiersza polecenia Hadoop](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Ładowanie danych do tabel gałęzi według partycji
> [!NOTE]
> To zadanie jest zazwyczaj dla administratora.
> 
> 

Zestaw danych taksówek NYC ma naturalne partycjonowanie według miesiąca, którego używamy, aby umożliwić szybsze przetwarzanie i czasy zapytań. Następujące polecenia programu PowerShell (wydane z katalogu Hive przy użyciu wiersza polecenia Hadoop) ładują dane do tabel trip i fare Hive, podzielonych na partycje według miesięcy.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

**Przykładowe\_dane\_\_\_ładowania\_gałęzi przez plik partitions.hql** zawiera następujące polecenia **LOAD:**

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Liczba zapytań hive używane w tym miejscu w procesie eksploracji obejmują patrząc na tylko jedną lub dwie partycje. Ale te zapytania mogą być uruchamiane w całym zestawie danych.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Pokazywale baz danych w klastrze programu HDInsight Hadoop
Aby wyświetlić bazy danych utworzone w klastrze Programu HDInsight Hadoop w oknie wiersza polecenia Hadoop, uruchom następujące polecenie w wierszu polecenia Hadoop:

    hive -e "show databases;"

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Pokaż tabele gałęzi w bazie danych **nyctaxidb**
Aby wyświetlić tabele w bazie danych **nyctaxidb,** uruchom następujące polecenie w wierszu polecenia Hadoop:

    hive -e "show tables in nyctaxidb;"

Możemy potwierdzić, że tabele są podzielone na partycje, uruchamiając następujące polecenie:

    hive -e "show partitions nyctaxidb.trip;"

Oto oczekiwane dane wyjściowe:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

Podobnie możemy zapewnić, że tabela taryf jest podzielona na partycje, uruchamiając następujące polecenie:

    hive -e "show partitions nyctaxidb.fare;"

Oto oczekiwane dane wyjściowe:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Eksploracja danych i inżynieria funkcji w Hive
> [!NOTE]
> Jest to zazwyczaj zadanie analityka danych.
> 
> 

Za pomocą zapytań hive można wykonywać eksplorację danych i zadania inżynieryjne funkcji dla danych załadowanych do tabel gałęzi. Oto przykłady takich zadań:

* Wyświetl 10 najlepszych rekordów w obu tabelach.
* Eksploruj dystrybucje danych w kilku polach w różnych oknach czasowych.
* Zbadaj jakość danych pól długości i szerokości geograficznej.
* Generowanie etykiet klasyfikacji binarnej i wieloklasowej na podstawie kwoty końcówki.
* Generowanie funkcji przez obliczanie bezpośrednich odległości podróży.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Eksploracja: zobacz 10 najlepszych rekordów w podróży po stole
> [!NOTE]
> Jest to zazwyczaj zadanie analityka danych.
> 
> 

Aby zobaczyć, jak wyglądają dane, sprawdź 10 rekordów z każdej tabeli. Aby sprawdzić rekordy, uruchom następujące dwa zapytania oddzielnie od wiersza katalogu Hive w konsoli wiersza polecenia Hadoop.

Aby uzyskać 10 najlepszych rekordów w tabeli podróży z pierwszego miesiąca:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Aby uzyskać 10 najlepszych rekordów w tabeli taryf od pierwszego miesiąca:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Rekordy można zapisać w pliku w celu wygodnego przeglądania przy niewielkiej zmianie w poprzednim zapytaniu:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Eksploracja: wyświetl liczbę rekordów w każdej z 12 partycji
> [!NOTE]
> Jest to zazwyczaj zadanie analityka danych.
> 
> 

Interesujące jest to, jak liczba podróży zmienia się w ciągu roku kalendarzowego. Grupowanie według miesiąca pokazuje rozkład podróży.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

To polecenie generuje następujące dane wyjściowe:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

W tym miejscu pierwsza kolumna to miesiąc, a druga to liczba podróży dla tego miesiąca.

Możemy również policzyć całkowitą liczbę rekordów w naszym zestawie danych podróży, uruchamiając następujące polecenie w wierszu katalogu Hive:

    hive -e "select count(*) from nyctaxidb.trip;"

To polecenie daje:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Za pomocą poleceń podobnych do tych wyświetlanych dla zestawu danych podróży, możemy wystawiać zapytania hive z monitu katalogu Hive dla zestawu danych taryfy, aby sprawdzić liczbę rekordów.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

To polecenie generuje to dane wyjściowe:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Dokładnie taka sama liczba podróży miesięcznie jest zwracana dla obu zestawów danych, zapewniając pierwszą weryfikację, że dane zostały poprawnie załadowane.

Całkowitą liczbę rekordów w zestawie danych taryfy można policzyć za pomocą następującego polecenia z wiersza polecenia katalogu Hive:

    hive -e "select count(*) from nyctaxidb.fare;"

To polecenie daje:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Całkowita liczba rekordów w obu tabelach jest również taka sama, zapewniając drugie sprawdzenie, czy dane zostały poprawnie załadowane.

### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Dystrybucja podróży przez medalion
> [!NOTE]
> Ta analiza jest zazwyczaj zadaniem analityka danych.
> 
> 

W tym przykładzie identyfikuje medaliony (numery taksówek) z większą niż 100 podróży w danym okresie czasu. Kwerenda korzysta z podzielonego na partycje dostępu do tabeli, ponieważ jest uwarunkowana przez **miesiąc**zmiennej partycji . Wyniki kwerendy są zapisywane w pliku lokalnym, **queryoutput.tsv**, w `C:\temp` w węźle głównym.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Oto treść **próbki\_ula\_podróży\_\_liczyć\_przez medalion.hql** pliku do kontroli.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medalion w zestawie danych taksówek NYC identyfikuje unikalną kabinę. Możesz określić, które kabiny są stosunkowo zajęte, pytając, które z nich wykonały więcej niż określoną liczbę podróży w danym okresie. Poniższy przykład identyfikuje kabiny, które wykonały więcej niż sto podróży w ciągu pierwszych trzech miesięcy, i zapisuje wyniki kwerendy w pliku lokalnym **C:\temp\queryoutput.tsv**.

Oto treść **próbki\_ula\_podróży\_\_liczyć\_przez medalion.hql** pliku do kontroli.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

W wierszu katalogu Hive uruchom następujące polecenie:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Eksploracja: Dystrybucja podróży przez medalion i licencję hack
> [!NOTE]
> To zadanie jest zazwyczaj dla analityka danych.
> 
> 

Podczas eksplorowania zestawu danych często chcemy zbadać rozkłady grup wartości. W tej sekcji przedstawiono przykład, jak wykonać tę analizę dla kabin i kierowców.

**Przykładowa\_liczba\_\_\_podróży\_ula\_według pliku medalion license.hql** grupuje zestaw danych taryfy na **medalion** i **hack_license**i zwraca liczbę każdej kombinacji. Oto jego zawartość:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

To zapytanie zwraca kombinacje kabiny i kierowcy, uporządkowane przez malejącą liczbę przejazdów.

Z monitu katalogu hive uruchom:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Wyniki kwerendy są zapisywane w pliku lokalnym **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Eksploracja: ocena jakości danych przez sprawdzenie nieprawidłowych rekordów długości i szerokości geograficznej
> [!NOTE]
> Jest to zazwyczaj zadanie analityka danych.
> 
> 

Wspólnym celem analizy danych badawczych jest odchwalenie nieprawidłowych lub złych zapisów. Przykład w tej sekcji określa, czy pola długości geograficznej lub szerokości geograficznej zawierają wartość daleko poza obszarem NYC. Ponieważ jest prawdopodobne, że takie rekordy mają błędną wartość długości i szerokości geograficznej, chcemy wyeliminować je z wszelkich danych, które mają być używane do modelowania.

Oto treść **próbki\_hive\_\_quality assessment.hql** pliku do wglądu.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Z monitu katalogu hive uruchom:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Argument *-S* zawarty w tym poleceniu pomija wydruk ekranu stanu zadań Mapy gałęzi/Zmniejszenia. To polecenie jest przydatne, ponieważ sprawia, że drukowanie ekranu danych wyjściowych zapytania hive jest bardziej czytelne.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Eksploracja: Binarne rozkłady klas porad dotyczących podróży
> [!NOTE]
> Jest to zazwyczaj zadanie analityka danych.
> 
> 

W przypadku problemu klasyfikacji binarnej opisane w [przykłady zadań przewidywania](hive-walkthrough.md#mltasks) sekcji, warto wiedzieć, czy wskazówka została podana, czy nie. Ta dystrybucja porad jest binarna:

* wskazówka (klasa 1, kwota napiwku\_> $0)  
* brak napiwku (klasa 0, kwota końcówki\_= $0)

Następujący **przykładowy\_plik\_z końcówką.hql znajduje się następujący przykładowy\_plik z końcówką.hql** pokazuje polecenie do uruchomienia:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Z monitu katalogu hive uruchom:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Eksploracja: Rozkłady klas w ustawieniu wieloklasowym
> [!NOTE]
> Jest to zazwyczaj zadanie analityka danych.
> 
> 

W przypadku problemu klasyfikacji wieloklasowej opisanego w sekcji [Przykłady zadań przewidywania](hive-walkthrough.md#mltasks) ten zestaw danych również nadaje się do naturalnej klasyfikacji, aby przewidzieć ilość podanych wskazówek. Możemy użyć pojemników do definiowania zakresów końcówki w zapytaniu. Aby uzyskać rozkłady klas dla różnych zakresów końcówek, użyj pliku **zakres\_\_\_\_końcówek ula próbki.** Oto jego zawartość.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Uruchom następujące polecenie z konsoli wiersza polecenia Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Eksploracja: obliczanie bezpośredniej odległości między dwoma lokalizacjami długości i szerokości geograficznej
> [!NOTE]
> Jest to zazwyczaj zadanie analityka danych.
> 
> 

Warto wiedzieć, czy istnieje różnica między bezpośrednią odległość między dwoma lokalizacjami, a rzeczywistą odległość podróży taksówki. Pasażer może być mniej skłonny do napiwku, jeśli dowie się, że kierowca celowo zabrał ich dłuższą trasą.

Aby zobaczyć porównanie rzeczywistej odległości podróży z [odległością Haversine](https://en.wikipedia.org/wiki/Haversine_formula) między dwoma punktami długości geograficznej (odległość "wielkiego okręgu", można użyć funkcji trygonometrycznych dostępnych w hive:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

W poprzednim zapytaniu R jest promieniem Ziemi w milach, a pi jest konwertowany na radiany. Punkty długości geograficznej szerokości geograficznej są filtrowane w celu usunięcia wartości, które są daleko od obszaru NYC.

W takim przypadku zapisujemy wyniki do katalogu o nazwie **queryoutputdir**. Sekwencja następujących poleceń najpierw tworzy ten katalog wyjściowy, a następnie uruchamia polecenie Hive.

Z monitu katalogu hive uruchom:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Wyniki kwerendy są zapisywane do dziewięciu obiektów blob platformy Azure (**queryoutputdir/000000\_0** do **queryoutputdir/000008\_0**), w domyślnym kontenerze klastra Hadoop.

Aby wyświetlić rozmiar poszczególnych obiektów blob, uruchom następujące polecenie z wiersza katalogu Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Aby wyświetlić zawartość danego pliku, powiedzmy **\_000000 0** `copyToLocal` , użyj polecenia Hadoop.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal`może być bardzo powolny dla dużych plików i nie jest zalecany do użytku z nimi.  
> 
> 

Kluczową zaletą posiadania tych danych znajduje się w obiekcie blob platformy Azure jest, że możemy eksplorować dane w ramach usługi Machine Learning, przy użyciu [modułu Importuj dane.][import-data]

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Dane w dół przykładowe i tworzenie modeli w uczeniu maszynowym
> [!NOTE]
> Jest to zazwyczaj zadanie analityka danych.
> 
> 

Po fazie analizy danych odkrywczych jesteśmy teraz gotowi do pobierania próbek w dół danych do tworzenia modeli w uęziemia machine learning. W tej sekcji pokazano, jak używać zapytania hive do pobierania próbek w dół danych. Uczenie maszynowe następnie uzyskuje do niego dostęp z modułu [Importuj dane.][import-data]

### <a name="down-sampling-the-data"></a>Pobieranie próbek w dół danych
Istnieją dwa kroki w tej procedurze. Najpierw dołączamy do **nyctaxidb.trip** i **nyctaxidb.fare** tabel na trzech kluczy, które są obecne we wszystkich rekordach: **medalion**, **licencja hack\_** i **datetime odbioru\_**. Następnie generujemy etykietę klasyfikacji binarnej, **przechyloną**i wieloklasową etykietę klasyfikacji, **klasę\_końcówki.**

Aby móc używać danych próbkowania w dół bezpośrednio z [modułu Importuj dane][import-data] w uściśliku maszynowym, należy przechowywać wyniki poprzedniej kwerendy w wewnętrznej tabeli gałęzi. W dalszej części utworzymy wewnętrzną tabelę hive i wypełniamy jej zawartość połączonymi i próbkowanymi danymi.

Kwerenda stosuje standardowe funkcje hive bezpośrednio do generowania następujących parametrów czasu z pola **datatime\_odbioru:**
- godzina dnia
- tydzień roku
- w dni powszednie ('1' oznacza poniedziałek, a "7" oznacza niedzielę)

Kwerenda generuje również bezpośrednią odległość między lokalizacjami pobrania i dropoff. Aby uzyskać pełną listę takich funkcji, zobacz [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Następnie kwerenda pobiera próbki danych w dół, aby wyniki kwerendy mogły zmieścić się w usłudze Azure Machine Learning Studio. Tylko około 1 procent oryginalnego zestawu danych jest importowany do studia.

Oto zawartość **przykładowego\_\_gałąź przygotować do\_\_pliku aml\_full.hql,** który przygotowuje dane do tworzenia modelu w umiań maszynowych:

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Aby uruchomić tę kwerendę z monitu katalogu gałęzi:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Mamy teraz wewnętrzną **tabelę, nyctaxidb.nyctaxi_downsampled_dataset**, do której można uzyskać dostęp za pomocą [modułu Importuj dane][import-data] z usługi Machine Learning. Ponadto możemy użyć tego zestawu danych do tworzenia modeli uczenia maszynowego.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Użyj modułu Importuj dane w u administratorze usługi Machine Learning, aby uzyskać dostęp do danych próbkowania w dół
Aby wystawiać zapytania hive w module [Importuj dane][import-data] uczenia maszynowego, musisz mieć dostęp do obszaru roboczego uczenia maszynowego. Potrzebny jest również dostęp do poświadczeń klastra i skojarzonego z nim konta magazynu.

Oto kilka szczegółów dotyczących modułu [Importuj dane][import-data] i parametrów do wprowadzenia:

**HCatalog server URI**: Jeśli nazwa klastra jest **abc123**, a następnie użyć: https:\//abc123.azurehdinsight.net.

**Nazwa konta użytkownika Hadoop:** Nazwa użytkownika wybrana dla klastra (nie nazwa użytkownika dostępu zdalnego).

**Hasło konta użytkownika Hadoop:** Hasło wybrane dla klastra (nie hasło dostępu zdalnego).

**Lokalizacja danych wyjściowych:** wybrana jako Azure.

**Nazwa konta usługi Azure Storage:** nazwa domyślnego konta magazynu skojarzonego z klastrem.

**Nazwa kontenera platformy Azure:** domyślna nazwa kontenera dla klastra i jest zazwyczaj taka sama jak nazwa klastra. W przypadku klastra o nazwie **abc123**nazwa to abc123.

> [!IMPORTANT]
> Każda tabela, którą chcemy zbadać przy użyciu modułu [Importuj dane][import-data] w uczeniu maszynowym, musi być tabelą wewnętrzną.
> 
> 

Oto jak ustalić, czy tabela **T** w bazie danych **D.db** jest tabelą wewnętrzną. W wierszu katalogu Hive uruchom następujące polecenie:

    hdfs dfs -ls wasb:///D.db/T

Jeśli tabela jest tabelą wewnętrzną i jest wypełniona, jej zawartość musi być pokazana w tym miejscu.

Innym sposobem określenia, czy tabela jest tabelą wewnętrzną, jest użycie Eksploratora usługi Azure Storage. Służy do przechodzenia do domyślnej nazwy kontenera klastra, a następnie filtrowania według nazwy tabeli. Jeśli tabela i jej zawartość są wyświetlane, potwierdza to, że jest to tabela wewnętrzna.

Oto zrzut ekranu zapytania hive i [modułu Importuj dane:][import-data]

![Zrzut ekranu przedstawiający kwerendę hive dla modułu Importuj dane](./media/hive-walkthrough/1eTYf52.png)

Ponieważ nasze dane próbkowania w dół znajduje się w domyślnym kontenerze, wynikowe zapytanie hive z usługi Machine Learning jest proste. To jest tylko **SELECT * Z nyctaxidb.nyctaxi\_downsampled\_danych**.

Zestaw danych może teraz służyć jako punkt wyjścia do tworzenia modeli uczenia maszynowego.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Tworzenie modeli w uczeniu maszynowym
Teraz można przystąpić do tworzenia modelu i wdrażania modelu w [ucząc usługi Machine Learning](https://studio.azureml.net). Dane są gotowe do wykorzystania w rozwiązywaniu problemów z przewidywaniem zidentyfikowanych wcześniej:

- **Klasyfikacja binarna**: Aby przewidzieć, czy napiwek został opłacony za podróż.

  **Uczący się używany:** Regresja logistyczna dwuklasowa

  a. W przypadku tego problemu etykieta docelowa (lub klasa) jest **przechylona**. Oryginalny zestaw danych z próbką w dół ma kilka kolumn, które są przeciekami docelowymi dla tego eksperymentu klasyfikacji. W szczególności **\_klasa końcówki,** **ilość\_końcówki**i całkowita **\_kwota** ujawniają informacje o etykiecie docelowej, która nie jest dostępna w czasie testowania. Usuwamy te kolumny z rozpatrzenia przy użyciu [wybierz kolumny w module zestawu danych.][select-columns]

  Na poniższym diagramie przedstawiono nasz eksperyment, aby przewidzieć, czy wskazówka została opłacona za daną podróż:

  ![Diagram eksperymentu do przewidzenia, czy wskazówka została zapłacona](./media/hive-walkthrough/QGxRz5A.png)

  b. W tym eksperymencie nasze docelowe dystrybucje etykiet były około 1:1.

   Na poniższym wykresie przedstawiono rozkład etykiet klasy końcówki dla problemu klasyfikacji binarnej:

  ![Wykres rozkładu etykiet klas końcówki](./media/hive-walkthrough/9mM4jlD.png)

    W rezultacie uzyskujemy obszar pod krzywą (AUC) 0,987, jak pokazano na poniższym rysunku:

  ![Wykres wartości AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Klasyfikacja wieloklasowa**: Aby przewidzieć zakres kwot napiwków zapłaconych za podróż, przy użyciu wcześniej zdefiniowanych klas.

  **Uczący się używany:** Wieloklasowa regresja logistyczna

  a. W przypadku tego problemu etykieta docelowa (lub klasa) to **klasa końcówki,\_** która może przyjąć jedną z pięciu wartości (0,1,2,3,4). Podobnie jak w przypadku klasyfikacji binarnej, mamy kilka kolumn, które są przecieki docelowe dla tego eksperymentu. W szczególności **przechylona,** **kwota\_końcówki**i całkowita **\_kwota** ujawniają informacje o etykiecie docelowej, która nie jest dostępna w czasie testowania. Usuwamy te kolumny za pomocą [modułu Wybierz kolumny w zestawie danych.][select-columns]

  Na poniższym diagramie przedstawiono eksperyment do przewidywania, w którym pojemniku końcówka może spaść. Kosze to: Klasa 0: wskazówka = $0, Klasa 1: wskazówka > $0 i wskazówka <= $5, Klasa 2: wskazówka > $5 i wskazówka <= $10, Klasa 3: wskazówka > $10 i wskazówka <= $20, a Klasa 4: wskazówka > $20.

  ![Diagram eksperymentu do przewidywania pojemnika dla końcówki](./media/hive-walkthrough/5ztv0n0.png)

  Teraz pokazujemy, jak wygląda rzeczywisty rozkład klasy testowej. Klasa 0 i klasa 1 są powszechne, a inne klasy są rzadkie.

  ![Wykres rozkładu klas testowych](./media/hive-walkthrough/Vy1FUKa.png)

  b. W tym eksperymencie używamy matrycy pomyłek, aby przyjrzeć się dokładnościom przewidywania, jak pokazano poniżej:

  ![Macierz zamieszania](./media/hive-walkthrough/cxFmErM.png)

  Podczas gdy dokładność klasy na rozpowszechnionych klas są dobre, model nie robi dobrą robotę "uczenia się" na rzadsze klasy.

- **Zadanie regresji:** Aby przewidzieć kwotę napiwku zapłaconego za podróż.

  **Uczący się używany:** Wzmocnione drzewo decyzyjne

  a. W przypadku tego problemu etykietą docelową (lub klasą) jest **kwota końcówki\_**. Docelowe przecieki w tym przypadku to: **przechylony,** **\_klasa końcówki**i całkowita **\_kwota.** Wszystkie te zmienne ujawniają informacje o ilości porad, która jest zazwyczaj niedostępna w czasie testowania. Usuwamy te kolumny za pomocą [modułu Wybierz kolumny w zestawie danych.][select-columns]

  Na poniższym diagramie przedstawiono eksperyment, aby przewidzieć ilość danej końcówki:

  ![Diagram eksperymentu do przewidywania ilości końcówki](./media/hive-walkthrough/11TZWgV.png)

  b. W przypadku problemów z regresją mierzymy dokładność przewidywania, patrząc na kwadratowy błąd w przewidywaniach i współczynnik determinacji:

  ![Zrzut ekranu przedstawiający statystyki przewidywania](./media/hive-walkthrough/Jat9mrz.png)

  W tym przypadku współczynnik oznaczania wynosi 0,709, co oznacza, że około 71 procent wariancji jest wyjaśnione przez współczynniki modelu.

> [!IMPORTANT]
> Aby dowiedzieć się więcej o uczeniu maszynowym oraz o tym, jak uzyskać do niego dostęp i z niego korzystać, zobacz [Co to jest uczenie maszynowe](../studio/what-is-machine-learning.md). Ponadto galeria [sztucznej inteligencji platformy Azure](https://gallery.cortanaintelligence.com/) obejmuje gamę eksperymentów i zapewnia dokładne wprowadzenie do zakresu możliwości uczenia maszynowego.
> 
> 

## <a name="license-information"></a>Informacje o licencji
Ten przykładowy instruktaż i towarzyszące mu skrypty są udostępniane przez firmę Microsoft na licencji MIT. Aby uzyskać więcej informacji, zobacz plik **LICENSE.txt** w katalogu przykładowego kodu w usłudze GitHub.

## <a name="references"></a>Dokumentacja
• [Andrés Monroy NYC Taxi Trips Strona pobierania](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC's Taxi Trip Dane Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi i Limousine Komisji Badań i Statystyk](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



