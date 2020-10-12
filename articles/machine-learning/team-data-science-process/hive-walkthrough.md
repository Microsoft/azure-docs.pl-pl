---
title: Eksplorowanie danych w klastrze usługi Hadoop — proces nauki danych zespołu
description: W celu uzyskania kompleksowego scenariusza korzystającego z analizy danych zespołu, można utworzyć i wdrożyć model przy użyciu klastra usługi HDInsight Hadoop.
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
ms.openlocfilehash: 991e81c46a0cd6c587ac3366b63ba4da6a07f7e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336517"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Proces nauki danych zespołu w działaniu: Użyj klastrów Azure HDInsight Hadoop
W tym instruktażu będziemy używać [procesu nauki o danych zespołowych (przetwarzania TDSP)](overview.md) w kompleksowym scenariuszu. Korzystamy z [klastra Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorowania i tworzenia funkcji danych z dostępnych publicznie zestawów danych [podróży NYCych](https://www.andresmh.com/nyctaxitrips/) w postaci pośrednich oraz do próbkowania danych. Aby obsłużyć wieloklasowe i wieloklasy klasyfikacje i regresje zadań predykcyjnych, tworzymy modele danych za pomocą Azure Machine Learning. 

Aby zapoznać się z przewodnikiem, który pokazuje, jak obsłużyć większy zestaw danych, zobacz [zespołowe przetwarzanie danych — używanie klastrów Azure HDInsight Hadoop w zestawie danych 1 TB](hive-criteo-walkthrough.md).

Za pomocą notesu IPython można także wykonać zadania przedstawione w przewodniku, który używa zestawu danych o pojemności 1 TB. Aby uzyskać więcej informacji, zobacz [Przewodnik Criteo przy użyciu połączenia ODBC platformy Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Opis zestawu danych NYC taksówki
Dane dotyczące podróży z NYC taksówkami dotyczą 20 GB skompresowanych plików z wartościami rozdzielanymi przecinkami (CSV) (~ 48 GB nieskompresowanych). Ma więcej niż 173 000 000 pojedynczych podróży i obejmuje opłaty płatne za każdą podróż. Każdy rekord rejsu obejmuje pobieranie i Dropoff lokalizacji i godziny, numer licencji anonimowe () oraz numer Medallion (unikatowy identyfikator taksówki). Dane obejmują wszystkie podróże w roku 2013 i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

- Pliki CSV trip_data zawierają szczegóły podróży: liczbę pasażerów, punkty pobierania i Dropoff, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:

  `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

- Pliki CSV trip_fare zawierają szczegóły dotyczące opłat za każdą podróż: typ płatności, Wysokość opłaty, opłaty i podatki, porady i opłaty oraz łączną kwotę płatną. Oto kilka przykładowych rekordów:

  `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Unikatowy klucz do przyłączenia do \_ danych podróży i opłaty za podróż \_ są złożone z pól: Medallion, \_ licencja na hakery i Data i \_ godzina pobrania. Aby uzyskać wszystkie szczegóły dotyczące konkretnej podróży, wystarczy dołączyć te trzy klucze.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Przykłady zadań przewidywania
Określ rodzaj prognoz, które chcesz utworzyć na podstawie analizy danych, aby ułatwić wyjaśnienie wymaganych zadań procesów. Poniżej przedstawiono trzy przykłady problemów przewidywania, które są używane w tym instruktażu, na podstawie * \_ kwoty*pozostałej:

- **Klasyfikacja binarna**: przewidywanie, czy Porada została zapłacona za podróż. Oznacza to, że * \_ Kwota TIP* , która jest większa niż $0 jest dodatnim przykładem, podczas gdy pozostała *część $0 \_ * jest ujemna.

  - Klasa 0: tip_amount = $0
  - Klasa 1: tip_amount > $0

- **Klasyfikacja wieloklasowa**: przewidywanie zakresu kwot TIP płatnych za podróż. Podziel * \_ kwotę* na pięć klas:

  - Klasa 0: tip_amount = $0
  - Klasa 1: tip_amount > $0 i tip_amount <= $5
  - Klasa 2: tip_amount > $5 i tip_amount <= $10
  - Klasa 3: tip_amount > $10 i tip_amount <= $20
  - Klasa 4: tip_amount > $20

- **Zadanie regresji**: przewidywanie kwoty Porada płatnej dla podróży.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Skonfiguruj klaster usługi HDInsight Hadoop na potrzeby zaawansowanej analizy
> [!NOTE]
> Jest to zazwyczaj zadanie administracyjne.
> 
> 

Środowisko platformy Azure można skonfigurować dla zaawansowanej analizy, która korzysta z klastra usługi HDInsight w trzech krokach:

1. [Utwórz konto magazynu](../../storage/common/storage-account-create.md): to konto magazynu jest używane do przechowywania danych w usłudze Azure Blob Storage. Dane używane w klastrach usługi HDInsight znajdują się również w tym miejscu.
2. [Dostosowywanie klastrów Azure HDInsight Hadoop na potrzeby zaawansowanego procesu i technologii analitycznej](customize-hadoop-cluster.md). W tym kroku jest tworzony klaster usługi HDInsight Hadoop z 64-bitową Anaconda Python 2,7 zainstalowany na wszystkich węzłach. Należy pamiętać o dwóch istotnych krokach, które należy wziąć pod uwagę podczas dostosowywania klastra usługi HDInsight.
   
   * Pamiętaj, aby połączyć konto magazynu utworzone w kroku 1 z klastrem usługi HDInsight podczas jego tworzenia. To konto magazynu uzyskuje dostęp do danych przetworzonych w ramach klastra.
   * Po utworzeniu klastra Włącz dostęp zdalny do węzła głównego klastra. Przejdź do karty **Konfiguracja** i wybierz pozycję **Włącz zdalne**. Ten krok określa poświadczenia użytkownika służące do logowania zdalnego.
3. [Utwórz obszar roboczy Azure Machine Learning](../classic/create-workspace.md): ten obszar roboczy służy do kompilowania modeli uczenia maszynowego. To zadanie jest rozkierowane po zakończeniu początkowej eksploracji danych i pobraniu próbek przy użyciu klastra usługi HDInsight.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Pobieranie danych z publicznego źródła
> [!NOTE]
> Jest to zazwyczaj zadanie administracyjne.
> 
> 

Aby skopiować zestaw danych [podróży z NYC taksówkami](https://www.andresmh.com/nyctaxitrips/) do maszyny z lokalizacji publicznej, użyj dowolnej z metod opisanych w temacie [przenoszenie danych do i z usługi Azure Blob Storage](move-azure-blob.md).

Tutaj opisano, jak używać AzCopy do przenoszenia plików zawierających dane. Aby pobrać i zainstalować AzCopy, postępuj zgodnie z instrukcjami podanymi na stronie [wprowadzenie do narzędzia wiersza polecenia AzCopy](../../storage/common/storage-use-azcopy.md).

1. W oknie wiersza polecenia Uruchom następujące polecenia AzCopy, zastępując *\<path_to_data_folder>* je pożądanym miejscem docelowym:

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

1. Po zakończeniu kopiowania w wybranym folderze danych zostanie wyświetlona suma z 24 plików spakowanych. Rozpakuj pobrane pliki do tego samego katalogu na komputerze lokalnym. Zanotuj folder, w którym znajdują się nieskompresowane pliki. Ten folder jest określany *\<path\_to\_unzipped_data\_files\>* w następujący sposób.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Przekaż dane do domyślnego kontenera klastra usługi HDInsight Hadoop
> [!NOTE]
> Jest to zazwyczaj zadanie administracyjne.
> 
> 

W następujących poleceniach AzCopy Zastąp następujące parametry wartościami, które zostały określone podczas tworzenia klastra usługi Hadoop i rozpakowywanie plików danych.

* ***\<path_to_data_folder>*** Katalog (wraz ze ścieżką) na komputerze, który zawiera niespakowane pliki danych.  
* ***\<storage account name of Hadoop cluster>*** Konto magazynu skojarzone z klastrem usługi HDInsight.
* ***\<default container of Hadoop cluster>*** Domyślny kontener używany przez klaster. Nazwa domyślnego kontenera jest zwykle taka sama jak nazwa klastra. Na przykład jeśli klaster ma nazwę "abc123.azurehdinsight.net", domyślnym kontenerem jest abc123.
* ***\<storage account key>*** Klucz dla konta magazynu używanego przez klaster.

W wierszu polecenia lub w oknie programu Windows PowerShell uruchom następujące dwa polecenia AzCopy.

To polecenie przekazuje dane podróży do katalogu ***nyctaxitripraw*** w domyślnym kontenerze klastra Hadoop.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
```

To polecenie przekazuje dane dotyczące taryfy do katalogu ***nyctaxifareraw*** w domyślnym kontenerze klastra Hadoop.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv
```

Dane powinny teraz znajdować się w magazynie obiektów blob i być gotowe do użycia w ramach klastra usługi HDInsight.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Zaloguj się do węzła głównego klastra usługi Hadoop i przygotuj się do analizy danych w sposób naukowy
> [!NOTE]
> Jest to zazwyczaj zadanie administracyjne.
> 
> 

Aby uzyskać dostęp do węzła głównego klastra w celu analizy danych i próbkowania danych, postępuj zgodnie z procedurą przedstawioną w temacie [dostęp do węzła głównego klastra usługi Hadoop](customize-hadoop-cluster.md).

W tym instruktażu przede wszystkim korzystamy z zapytań utworzonych w programie [Hive](https://hive.apache.org/), języka zapytań PRZYPOMINAJĄCYCH kod SQL, aby przeprowadzać wstępne eksploracje danych. Zapytania programu Hive są przechowywane w plikach ". HQL". Następnie przeprowadzimy próbkowanie tych danych w Machine Learning do tworzenia modeli.

Aby przygotować klaster do analizy danych w sposób naukowy, Pobierz pliki ". HQL" zawierające odpowiednie skrypty Hive z usługi [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) do katalogu lokalnego (C:\Temp) w węźle głównym. Otwórz wiersz polecenia z poziomu węzła głównego klastra i uruchom następujące dwa polecenia:

```console
set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
```

Te dwa polecenia pobierają wszystkie pliki ". HQL", które są konieczne w tym instruktażu do katalogu lokalnego ***C:\temp&#92;*** w węźle głównym.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Tworzenie bazy danych i tabel programu Hive partycjonowane według miesiąca
> [!NOTE]
> To zadanie jest zazwyczaj przeznaczone dla administratora.
> 
> 

Teraz można przystąpić do tworzenia tabel programu Hive dla zestawu danych NYC Taksówk.
W węźle głównym klastra usługi Hadoop Otwórz wiersz polecenia usługi Hadoop na pulpicie węzła głównego. Wprowadź katalog Hive, uruchamiając następujące polecenie:

```console
cd %hive_home%\bin
```

> [!NOTE]
> Uruchom wszystkie polecenia Hive w tym instruktażu w wierszu bin/Directory programu Hive. Obsługuje to automatyczne problemy ze ścieżką. W tym instruktażu są używane terminy "wiersz usługi Hive", "wiersz bin/katalog Hive" oraz "wiersz polecenia usługi Hadoop".
> 
> 

W wierszu polecenia usługi Hive, uruchom następujące polecenie w wierszu poleceń Hadoop węzła głównego, który tworzy bazę danych i tabele programu Hive:

```console
hive -f "C:\temp\sample_hive_create_db_and_tables.hql"
```

Poniżej znajduje się zawartość pliku **C:\temp\sample \_ Hive \_ Create DB i Tables \_ \_ \_ . HQL** , który tworzy bazę danych Hive **nyctaxidb**oraz informacje o wykorzystaniu i **obtaryfie** **tabel.**

```hiveql
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
```

Ten skrypt programu Hive tworzy dwie tabele:

* Tabela **rejsów** zawiera szczegółowe informacje o podróży poszczególnych przebiegów (Szczegóły sterownika, czas pobierania, odległość podróży i godziny).
* Tabela **opłata** zawiera szczegółowe informacje o opłatach (opłata za opłatę, kwotę Porada, opłaty i opłaty).

Jeśli potrzebujesz dodatkowej pomocy z tymi procedurami lub chcesz zbadać alternatywy, zobacz sekcję [przesyłanie zapytań Hive bezpośrednio z wiersza polecenia usługi Hadoop](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Ładowanie danych do tabel programu Hive według partycji
> [!NOTE]
> To zadanie jest zazwyczaj przeznaczone dla administratora.
> 
> 

Zestaw danych NYC taksówki ma naturalny podział według miesiąca, którego używamy do szybszego przetwarzania i czasów wykonywania zapytań. Następujące polecenia programu PowerShell (wydawane z katalogu Hive przy użyciu wiersza polecenia Hadoop) ładują dane do tabel programu Hive w podróży i taryf, podzielone na partycje według miesiąca.

```powershell
for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")
```

**Przykładowa \_ gałąź \_ ładowania \_ danych \_ za pomocą pliku \_ partitions. HQL** zawiera następujące polecenia **ładowania** :

```hiveql
LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});
```

Liczba zapytań Hive użytych w tym miejscu w procesie eksploracji obejmuje tylko jedną lub dwie partycje. Jednak te zapytania można uruchamiać w całym zestawie danych.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Wyświetlanie baz danych w klastrze usługi HDInsight Hadoop
Aby wyświetlić bazy danych utworzone w klastrze usługi HDInsight Hadoop wewnątrz okna wiersza polecenia usługi Hadoop, uruchom następujące polecenie w wierszu polecenia usługi Hadoop:

```console
hive -e "show databases;"
```

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Pokaż tabele programu Hive w bazie danych **nyctaxidb**
Aby wyświetlić tabele w bazie danych **nyctaxidb** , uruchom następujące polecenie w wierszu polecenia usługi Hadoop:

```console
hive -e "show tables in nyctaxidb;"
```

Możemy potwierdzić, że tabele są podzielone na partycje, uruchamiając następujące polecenie:

```console
hive -e "show partitions nyctaxidb.trip;"
```

Oto oczekiwane dane wyjściowe:

```output
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
```

Podobnie możemy upewnić się, że tabela opłat jest podzielona na partycje, uruchamiając następujące polecenie:

```console
hive -e "show partitions nyctaxidb.fare;"
```

Oto oczekiwane dane wyjściowe:

```output
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
```

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Eksploracja danych i inżynieria funkcji w usłudze Hive
> [!NOTE]
> Jest to zwykle zadanie Analityka danych.
> 
> 

Zapytania Hive umożliwiają wykonywanie eksploracji danych i zadań inżynieryjnych dotyczących danych ładowanych do tabel programu Hive. Oto przykłady takich zadań:

* Wyświetl 10 pierwszych rekordów w obu tabelach.
* Eksplorowanie dystrybucji danych kilku pól w różnych oknach czasu.
* Zbadaj jakość danych pól długości i szerokości geograficznej.
* Generowanie etykiet klasyfikacji danych binarnych i wieloklasowych na podstawie kwoty pozostałej.
* Generuj funkcje, obliczając bezpośrednie odległości podróży.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Eksploracja: wyświetlanie 10 pierwszych rekordów w podróży tabeli
> [!NOTE]
> Jest to zwykle zadanie Analityka danych.
> 
> 

Aby zobaczyć, jak wyglądają dane, sprawdź 10 rekordów z każdej tabeli. Aby sprawdzić rekordy, uruchom następujące dwa zapytania oddzielnie od wiersza polecenia w katalogu Hive w konsoli programu Hadoop.

Aby uzyskać 10 pierwszych rekordów w tabeli rejsów z pierwszego miesiąca:

```console
hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
```

Aby uzyskać 10 pierwszych rekordów w tabeli opłat od pierwszego miesiąca:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;"
```

Rekordy można zapisać do pliku, aby wygodnie przeglądać z niewielką zmianą w powyższym zapytaniu:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput
```

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Eksploracja: wyświetlanie liczby rekordów w każdej z 12 partycji
> [!NOTE]
> Jest to zwykle zadanie Analityka danych.
> 
> 

Jest to sposób, w jaki liczba podróży jest różna w roku kalendarzowym. Grupowanie według miesiąca przedstawia rozkład podróży.

```console
hive -e "select month, count(*) from nyctaxidb.trip group by month;"
```

To polecenie tworzy następujące dane wyjściowe:

```output
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
```

W tym miejscu pierwsza kolumna to miesiąc, a druga to liczba podróży w danym miesiącu.

Możemy również policzyć całkowitą liczbę rekordów w naszym zestawie danych podróży, uruchamiając następujące polecenie w wierszu polecenia w katalogu Hive:

```console
hive -e "select count(*) from nyctaxidb.trip;"
```

To polecenie daje:

```output
173179759
Time taken: 284.017 seconds, Fetched: 1 row(s)
```

Przy użyciu poleceń podobnych do tych, które są wyświetlane dla zestawu danych dla podróży, możemy wydać zapytania Hive z poziomu wiersza polecenia w katalogu Hive, aby sprawdzić poprawność liczby rekordów.

```console
hive -e "select month, count(*) from nyctaxidb.fare group by month;"
```

To polecenie tworzy następujące dane wyjściowe:

```output
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
```

Dokładna liczba rejsów miesięcznie jest zwracana dla obu zestawów danych, co zapewnia pierwsze sprawdzenie poprawności, czy dane zostały załadowane poprawnie.

Możesz policzyć łączną liczbę rekordów w zestawie danych o taryfie przy użyciu następującego polecenia w wierszu katalogu programu Hive:

```console
hive -e "select count(*) from nyctaxidb.fare;"
```

To polecenie daje:

```output
173179759
Time taken: 186.683 seconds, Fetched: 1 row(s)
```

Całkowita liczba rekordów w obu tabelach jest również taka sama, co zapewnia drugą weryfikację, że dane zostały prawidłowo załadowane.

### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: dystrybucja podróży według Medallion
> [!NOTE]
> Ta analiza jest zazwyczaj zadaniem Analityka danych.
> 
> 

W tym przykładzie zidentyfikowano Medallions (liczby taksówki) o długości większej niż 100 podróży w danym przedziale czasu. Zapytanie ma zalety dostępu do partycjonowanej tabeli, ponieważ jest ono warunkiem **miesiąca**zmiennej partycji. Wyniki zapytania są zapisywane w pliku lokalnym, **queryoutput. tsv**, w węźle głównym `C:\temp` .

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

Poniżej znajduje się zawartość **przykładowej \_ \_ liczby rejsów usługi Hive \_ według pliku \_ \_ Medallion. HQL** w celu przeprowadzenia inspekcji.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Medallion w zestawie danych o NYCych taksówkach identyfikuje unikatowy plik cab. Można określić, które OOZ są podane w sposób porównawczy przez zapytanie, które z nich przeprowadzono więcej niż określoną liczbę podróży w określonym przedziale czasu. Poniższy przykład identyfikuje OOZ, którzy wykonali ponad setki podróży w ciągu pierwszych trzech miesięcy i zapisuje wyniki zapytania do pliku lokalnego, **C:\temp\queryoutput.tsv**.

Poniżej znajduje się zawartość **przykładowej \_ \_ liczby rejsów usługi Hive \_ według pliku \_ \_ Medallion. HQL** w celu przeprowadzenia inspekcji.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

W wierszu polecenia katalogu Hive Uruchom następujące polecenie:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Eksploracja: dystrybucja podróży według licencji Medallion i hakerów
> [!NOTE]
> To zadanie jest zwykle przeznaczone dla analityków danych.
> 
> 

Podczas eksplorowania zestawu danych często chcemy przeanalizować dystrybucje grup wartości. Ta sekcja zawiera przykład sposobu przeprowadzania tej analizy dla plików CAB i sterowników.

**Przykładowa \_ \_ Liczba rejsów Hive \_ \_ według \_ \_ licencji Medallion. plik HQL** grupuje zestaw danych taryfy dla **Medallion** i **hack_license**i zwraca liczbę każdej kombinacji. Oto zawartość:

```hiveql
SELECT medallion, hack_license, COUNT(*) as trip_count
FROM nyctaxidb.fare
WHERE month=1
GROUP BY medallion, hack_license
HAVING trip_count > 100
ORDER BY trip_count desc;
```

To zapytanie zwraca kombinacje plików CAB i sterowników uporządkowane według malejącej liczby rejsów.

W wierszu polecenia katalogu Hive Uruchom polecenie:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv
```

Wyniki zapytania są zapisywane w pliku lokalnym, **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Eksploracja: ocenianie jakości danych przez sprawdzanie nieprawidłowych długości geograficznych lub Latitude
> [!NOTE]
> Jest to zwykle zadanie Analityka danych.
> 
> 

Typowym celem analizy danych poznawczych jest odznaczenie nieprawidłowych lub uszkodzonych rekordów. W przykładzie w tej sekcji określa się, czy pola długości i szerokości geograficznej zawierają wartość daleko poza obszar NYC. Ponieważ istnieje najprawdopodobniej, że takie rekordy mają błędne wartości długości geograficznej, chcemy wyeliminować je z danych, które mają być używane do modelowania.

Poniżej znajduje się zawartość **przykładowego \_ pliku \_ oceny jakości usługi Hive \_ . HQL** do inspekcji.

```hiveql
    SELECT COUNT(*) FROM nyctaxidb.trip
    WHERE month=1
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);
```

W wierszu polecenia katalogu Hive Uruchom polecenie:

```console
hive -S -f "C:\temp\sample_hive_quality_assessment.hql"
```

Argument *-S* zawarty w tym poleceniu pomija wydruk ekranu stanu mapy Hive/Zmniejsz zadania. To polecenie jest przydatne, ponieważ sprawia, że ekran wydruku zapytania programu Hive jest bardziej czytelny.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Eksploracja: rozpowszechnianie klas binarnych porad podróży
> [!NOTE]
> Jest to zwykle zadanie Analityka danych.
> 
> 

W przypadku problemu klasyfikacji binarnej podanej w sekcji [przykłady zadań przewidywania](hive-walkthrough.md#mltasks) warto sprawdzić, czy podano poradę. Ta dystrybucja porad jest binarna:

* podaną wskazówką (Klasa 1, kwota Porada \_ > $0)  
* Brak Porada (klasa 0, \_ Kwota Porada = $0)

Następujące przykładowe przerzucane ** \_ \_ \_ częstotliwości. HQL** plik zawiera polecenie do uruchomienia:

```hiveql
SELECT tipped, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tipped;
```

W wierszu polecenia katalogu Hive Uruchom polecenie:

```console
hive -f "C:\temp\sample_hive_tipped_frequencies.hql"
```


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Eksploracja: dystrybucje klas w ustawieniu wieloklasowym
> [!NOTE]
> Jest to zwykle zadanie Analityka danych.
> 
> 

W przypadku problemu klasyfikacji wieloklasowej, którą opisano w sekcji [przykłady zadań przewidywania](hive-walkthrough.md#mltasks) , ten zestaw danych pozwala również na naturalną klasyfikację do przewidywania liczby podanych wskazówek. Za pomocą pojemników można definiować zakresy etykiet w zapytaniu. Aby uzyskać dystrybucje klas dla różnych zakresów etykiet, użyj pliku ** \_ \_ \_ \_ HQL** . Poniżej znajdują się jego zawartość.

```hiveql
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
```

Uruchom następujące polecenie w konsoli wiersza polecenia usługi Hadoop:

```console
hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"
```

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Eksploracja: Oblicz bezpośrednią odległość między dwiema lokalizacjami szerokości geograficznej
> [!NOTE]
> Jest to zwykle zadanie Analityka danych.
> 
> 

Warto wiedzieć, czy istnieje różnica między bezpośrednią odległością między dwiema lokalizacjami i rzeczywistą odległością podróży. Pasażer może być krótszy, aby pozostały niekorzystnie, jeśli połączyli się, że sterownik został celowo przejęty przez dłuższą trasę.

Aby zobaczyć porównanie między rzeczywistą odległością podróży a [odległością Haversine](https://en.wikipedia.org/wiki/Haversine_formula) między dwoma punktami szerokości geograficznej (odległości "doskonały okrąg"), można użyć funkcji trygonometrycznych dostępnych w ramach programu Hive:

```hiveql
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
```

W powyższym zapytaniu R jest promień ziemi w milach, a Pi jest konwertowany na radiany. Punkty szerokości geograficznej są filtrowane w celu usunięcia wartości, które są daleko od obszaru NYC.

W takim przypadku napiszemy wyniki do katalogu o nazwie **queryoutputdir**. Sekwencja następujących poleceń najpierw tworzy ten katalog wyjściowy, a następnie uruchamia polecenie Hive.

W wierszu polecenia katalogu Hive Uruchom polecenie:

```hiveql
hdfs dfs -mkdir wasb:///queryoutputdir

hive -f "C:\temp\sample_hive_trip_direct_distance.hql"
```

Wyniki zapytania są zapisywane do dziewięciu obiektów blob platformy Azure (**queryoutputdir/000000 \_ 0** do  **queryoutputdir/000008 \_ 0**) w obszarze domyślnego kontenera klastra Hadoop.

Aby wyświetlić rozmiar poszczególnych obiektów blob, uruchom następujące polecenie w wierszu polecenia katalogu Hive:

```hiveql
hdfs dfs -ls wasb:///queryoutputdir
```

Aby wyświetlić zawartość danego pliku, powiedz **000000 \_ 0**, użyj `copyToLocal` polecenia Hadoop.

```hiveql
hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile
```

> [!WARNING]
> `copyToLocal` mogą być bardzo wolne dla dużych plików i nie jest zalecane do użycia z nimi.  
> 
> 

Główną zaletą, że te dane znajdują się w obiekcie blob platformy Azure, można eksplorować dane w Machine Learning przy użyciu modułu [Importuj dane][import-data] .

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Przykładowe dane i modele kompilacji w Machine Learning
> [!NOTE]
> Jest to zwykle zadanie Analityka danych.
> 
> 

Po fazie analizy danych w trakcie określania wartości badawczych możesz teraz przystąpić do powyższego próbkowania danych do tworzenia modeli w Machine Learning. W tej sekcji pokazano, jak za pomocą zapytania programu Hive wykonać przykładowe dane. Machine Learning następnie uzyskuje dostęp do niego z modułu [Importuj dane][import-data] .

### <a name="down-sampling-the-data"></a>Podwyższenie poziomu próbkowanie danych
Ta procedura obejmuje dwie czynności. Najpierw dołączymy tabele **nyctaxidb. Trip** i **nyctaxidb. opłaty** za trzy klucze, które znajdują się we wszystkich rekordach: **Medallion**, ** \_ licencja na hakery**i **Data i \_ godzina pobrania**. Następnie generujemy binarną etykietę klasyfikacji, **przechyloną**i wieloklasową etykietę klasyfikacji, ** \_ klasę TIP**.

Aby można było korzystać z danych z próbką w dół bezpośrednio z modułu [Importuj dane][import-data] w Machine Learning, należy przechowywać wyniki poprzedniego zapytania w wewnętrznej tabeli programu Hive. W poniższym przykładzie utworzymy wewnętrzną tabelę programu Hive i zapełnimy jej zawartość danymi dołączonymi i w dół.

Zapytanie stosuje standardowe funkcje programu Hive bezpośrednio w celu wygenerowania następujących parametrów czasu w **polu \_ Data/godzina pobrania** :
- godzina dnia
- tydzień roku
- dzień tygodnia ("1" oznacza poniedziałek, a "7" oznacza niedzielę)

Zapytanie generuje również bezpośrednią odległość między lokalizacjami pobierania i Dropoff. Aby zapoznać się z pełną listą takich funkcji, zobacz [LANGUAGEMANUAL UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Zapytanie w dół przedstawia dane, dzięki czemu wyniki zapytania mogą pasować do Azure Machine Learning Studio. Do Studio importowanych jest tylko 1 procent oryginalnego zestawu danych.

Poniżej przedstawiono zawartość **przykładowego \_ \_ przygotowania do pliku \_ \_ AML \_ Full. HQL** , który przygotowuje dane do kompilowania modelu w Machine Learning:

```hiveql
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
```

Aby uruchomić to zapytanie z poziomu wiersza polecenia w katalogu Hive:

```console
hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"
```

Mamy teraz wewnętrzną tabelę **nyctaxidb.nyctaxi_downsampled_dataset**, do której można uzyskać dostęp za pomocą modułu [Import danych][import-data] z Machine Learning. Ponadto możemy użyć tego zestawu danych do kompilowania modeli Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Użyj modułu Importuj dane w Machine Learning, aby uzyskać dostęp do danych z próbką w dół
Aby wystawić zapytania Hive w module [importu danych][import-data] Machine Learning, musisz mieć dostęp do Machine Learning obszaru roboczego. Wymagany jest również dostęp do poświadczeń klastra i skojarzonego z nim konta magazynu.

Poniżej przedstawiono niektóre szczegóły dotyczące modułu [importowania danych][import-data] i parametrów do wprowadzenia:

**Identyfikator URI serwera HCatalog**: Jeśli nazwa klastra to **abc123**, użyj: https: \/ /abc123.azurehdinsight.NET.

**Nazwa konta użytkownika usługi Hadoop**: Nazwa użytkownika wybrana dla klastra (nie nazwa użytkownika dostępu zdalnego).

**Hasło konta użytkownika usługi Hadoop**: hasło wybrane dla klastra (nie hasło dostępu zdalnego).

**Lokalizacja danych wyjściowych**: wybrano jako platformę Azure.

**Nazwa konta usługi Azure Storage**: Nazwa domyślnego konta magazynu skojarzonego z klastrem.

**Nazwa kontenera platformy Azure**: domyślna nazwa kontenera dla klastra i jest zwykle taka sama jak nazwa klastra. W przypadku klastra o nazwie **abc123**nazwa to abc123.

> [!IMPORTANT]
> Każda tabela, do której chcemy wykonać zapytanie, przy użyciu modułu [Importuj dane][import-data] w Machine Learning musi być tabelą wewnętrzną.
> 
> 

Oto jak ustalić, czy tabela **T** w bazie danych **D. DB** jest tabelą wewnętrzną. W wierszu polecenia katalogu Hive Uruchom następujące polecenie:

```hiveql
hdfs dfs -ls wasb:///D.db/T
```

Jeśli tabela jest tabelą wewnętrzną i została wypełniona, jej zawartość musi być tutaj pokazana.

Innym sposobem określenia, czy tabela jest tabelą wewnętrzną, jest użycie Eksplorator usługi Azure Storage. Użyj go, aby przejść do domyślnej nazwy kontenera klastra, a następnie filtrować według nazwy tabeli. Jeśli tabela i jej zawartość są wyświetlane, potwierdza to, że jest to tabela wewnętrzna.

Poniżej znajduje się zrzut ekranu zapytania programu Hive i modułu [Importuj dane][import-data] :

![Zrzut ekranu przedstawiający zapytanie programu Hive dotyczące modułu importu danych](./media/hive-walkthrough/1eTYf52.png)

Ponieważ nasze dane w dalszej części znajdują się w domyślnym kontenerze, powstaje zapytanie Hive z Machine Learning jest proste. Jest to tylko **wybór * z nyctaxidb. nyctaxi \_ \_ danych z próbką**.

Zestaw danych może być teraz używany jako punkt początkowy do kompilowania modeli Machine Learning.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Tworzenie modeli w Machine Learning
Teraz możesz przystąpić do tworzenia modeli i wdrażania modelu w [Machine Learning](https://studio.azureml.net). Dane są gotowe do użycia w przypadku rozwiązywania określonych wcześniej problemów przewidywania:

- **Klasyfikacja binarna**: w celu przewidywania, czy Porada została zapłacona za podróż.

  **Używany przez Ciebie opis:** Regresja logistyczna dla dwóch klas

  a. W przypadku tego problemu, etykieta docelowa (lub klasy **tipped**) jest wyrzucana. Oryginalny, próbkowany zestaw danych zawiera kilka kolumn, które są docelowymi przeciekami dla tego eksperymentu klasyfikacji. W szczególności, ** \_ Klasa etykietki**, ** \_ Kwota Porada**i **łączna \_ Kwota** ujawniają informacje o etykiecie docelowej, która nie jest dostępna podczas testowania. Te kolumny są usuwane z rozważenia przy użyciu modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

  Na poniższym diagramie przedstawiono doświadczenia umożliwiające prognozowanie, czy Porada została zapłacona za daną podróż:

  ![Diagram eksperymentu do przewidywania w przypadku wypłaty porady](./media/hive-walkthrough/QGxRz5A.png)

  b. Dla tego eksperymentu nasze ukierunkowane etykiety zostały w przybliżeniu 1:1.

   Poniższy wykres pokazuje rozkład etykiet klas etykiet dla problemu klasyfikacji binarnej:

  ![Wykres dystrybucji etykiet klas etykietki](./media/hive-walkthrough/9mM4jlD.png)

    W związku z tym uzyskamy obszar pod krzywą (AUC) 0,987, jak pokazano na poniższym rysunku:

  ![Wykres wartości AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Klasyfikacja wieloklasowa**: aby przewidzieć zakres kwot TIP płatnych za podróż przy użyciu wcześniej zdefiniowanych klas.

  **Używany przez Ciebie opis:** Wieloklasowa regresja logistyczna

  a. W przypadku tego problemu, nasza etykieta docelowa (lub klasy) to ** \_ Klasa etykiet**, która może przyjmować jedną z pięciu wartości (0, 1, 2, 3, 4). Podobnie jak w przypadku klasyfikacji binarnej, mamy kilka kolumn, które są docelowymi przeciekami dla tego eksperymentu. W szczególności **przechylona**, ** \_ Kwota Porada**i **łączna \_ Kwota** ujawniają informacje o etykiecie docelowej, która nie jest dostępna podczas testowania. Te kolumny zostaną usunięte przy użyciu modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

  Na poniższym diagramie przedstawiono eksperyment do przewidywania, w którym pojemniku może spaść Porada. Pojemniki: Klasa 0: TIP = $0, Klasa 1: TIP > $0 i TIP <= $5, Klasa 2: TIP > $5 i TIP <= $10, Klasa 3: TIP > $10 i TIP <= $20 i Klasa 4: TIP > $20.

  ![Diagram eksperymentu do przewidywania pojemników dla wskazówki](./media/hive-walkthrough/5ztv0n0.png)

  Teraz pokazujemy, jak wygląda rzeczywista dystrybucja klasy testowej. Klasy 0 i 1 są współznane, a inne klasy są rzadkie.

  ![Wykres dystrybucji klasy testowej](./media/hive-walkthrough/Vy1FUKa.png)

  b. Na potrzeby tego eksperymentu używamy niezmienionej macierzy, aby przyjrzeć się dokładnościom przewidywania, jak pokazano poniżej:

  ![Macierz pomyłek](./media/hive-walkthrough/cxFmErM.png)

  Chociaż nieścisłości klas w przypadku znanych klas są dobre, model nie wykonuje dobrego zadania "Uczenie się" na klasach rzadkich.

- **Zadanie regresji**: przewidywanie kwoty Porada płatnej dla podróży.

  **Używany przez Ciebie opis:** Drzewo podwyższanych decyzji

  a. W przypadku tego problemu etykieta Target (lub Class) to ** \_ Kwota Porada**. Przecieki docelowe w tym przypadku to: **przechylony**, ** \_ Klasa TIP**i **łączna \_ Kwota**. Wszystkie te zmienne ujawniają informacje na temat kwoty TIP, która jest zwykle niedostępna w czasie testowania. Te kolumny zostaną usunięte przy użyciu modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

  Na poniższym diagramie przedstawiono eksperyment do przewidywania ilości danej pozostałej Porady:

  ![Diagram eksperymentu do przewidywania ilości pozostałej porady](./media/hive-walkthrough/11TZWgV.png)

  b. W przypadku problemów z regresją mierzy się kombinacje prognoz, patrząc na błąd kwadratowy w przewidywaniach i współczynnika wyznaczania:

  ![Zrzut ekranu przedstawiający statystyki prognoz](./media/hive-walkthrough/Jat9mrz.png)

  W tym miejscu współczynnika wyznaczania jest 0,709, co oznacza, że około 71 procent wariancji jest wyjaśniony przez współczynniki modelu.

> [!IMPORTANT]
> Aby dowiedzieć się więcej na temat Machine Learning i sposobu uzyskiwania dostępu do niego i używania go, zobacz [co to jest Machine Learning](../classic/index.yml). Ponadto [Azure AI Gallery](https://gallery.cortanaintelligence.com/) obejmuje gamę eksperymentów i zapewnia szczegółowe wprowadzenie do zakresu możliwości Machine Learning.
> 
> 

## <a name="license-information"></a>Informacje o licencji
Ten przykładowy przewodnik i towarzyszące mu skrypty są udostępniane przez firmę Microsoft w ramach licencji MIT. Aby uzyskać więcej informacji, zobacz plik **LICENSE.txt** w katalogu przykładowego kodu w witrynie GitHub.

## <a name="references"></a>Odwołania
•    [Strona pobierania Andrés MONROY NYC TRIPS](https://www.andresmh.com/nyctaxitrips/)  
•    [Dane dotyczące podróży z NYCą w folii przez Krzysztof Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [Badania i statystyka NYCych taksówki oraz Komisji Limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
