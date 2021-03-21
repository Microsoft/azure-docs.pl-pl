---
title: 'Samouczek: operacje ETL przy użyciu zapytania interaktywnego — Azure HDInsight'
description: Samouczek — informacje na temat wyodrębniania danych z nieprzetworzonego zestawu danych udostępnionych woluminów klastra. Przekształć je za pomocą interakcyjnego zapytania w usłudze HDInsight. Następnie załaduj przekształcone dane do Azure SQL Database przy użyciu platformy Apache Sqoop.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 07/02/2019
ms.openlocfilehash: 1aab6b4fe4ffafb3cbba60b2b3774d2577d3d53c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562471"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interakcyjnych zapytań w usłudze Azure HDInsight

W ramach tego samouczka pobierasz Nieprzetworzony plik danych CSV z publicznie dostępnych danych lotu. Zaimportuj go do magazynu klastra usługi HDInsight, a następnie Przekształć dane przy użyciu interakcyjnych zapytań w usłudze Azure HDInsight. Po przeprowadzeniu danych dane są ładowane do bazy danych w Azure SQL Database przy użyciu platformy [Apache Sqoop](https://sqoop.apache.org/).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Pobieranie przykładowych danych lotów
> * Przekazywanie danych do klastra usługi HDInsight
> * Przekształć dane przy użyciu zapytania interaktywnego
> * Tworzenie tabeli w bazie danych w Azure SQL Database
> * Użyj Sqoop do eksportowania danych do bazy danych w Azure SQL Database

## <a name="prerequisites"></a>Wymagania wstępne

* Interaktywny klaster zapytań w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybieranie **zapytania interaktywnego** dla **typu klastra**.

* Baza danych w Azure SQL Database. Baza danych jest używana jako docelowy magazyn danych. Jeśli nie masz bazy danych w Azure SQL Database, zobacz [Tworzenie bazy danych w Azure SQL Database w Azure Portal](../../azure-sql/database/single-database-create-quickstart.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Pobieranie danych lotów

1. Przejdź do strony [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?gnoyr_VQ=FGJ).

2. Na stronie Wyczyść wszystkie pola, a następnie wybierz następujące wartości:

   | Nazwa | Wartość |
   | --- | --- |
   | Rok filtrowania |2019 |
   | Okres filtrowania |January (Styczeń) |
   | Pola |`Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay`. |

3. Kliknij pozycję **Pobierz**. Uzyskasz plik zip z wybranymi polami danych.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Przekazywanie danych do klastra usługi HDInsight

Istnieje wiele sposobów przekazywania danych do magazynu skojarzonego z klastrem usługi HDInsight. W tej sekcji przekażesz dane za pomocą polecenia `scp`. Aby dowiedzieć się więcej o innych sposobach przekazywania danych, zobacz [Upload data to HDInsight (przekazywanie danych do usługi HDInsight)](../hdinsight-upload-data.md).

1. Przekaż plik zip do węzła głównego klastra usługi HDInsight. Edytuj poniższe polecenie, zastępując je `FILENAME` nazwą pliku zip i `CLUSTERNAME` nazwą klastra usługi HDInsight. Następnie otwórz wiersz polecenia, Ustaw katalog roboczy na lokalizację pliku, a następnie wprowadź polecenie.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Jeśli zostanie wyświetlony monit, wprowadź wartość tak lub nie, aby kontynuować. Tekst nie jest widoczny w oknie podczas pisania.

2. Po zakończeniu przekazywania połącz się z klastrem przy użyciu protokołu SSH. Edytuj poniższe polecenie, zastępując je `CLUSTERNAME` nazwą klastra usługi HDInsight. Wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Skonfiguruj zmienną środowiskową po nawiązaniu połączenia SSH. Zamień `FILE_NAME` , `SQL_SERVERNAME` , `SQL_DATABASE` , `SQL_USER` i `SQL_PASWORD` z odpowiednimi wartościami. Następnie wprowadź polecenie:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Rozpakuj plik. zip, wprowadzając poniższe polecenie:

    ```bash
    unzip $FILENAME.zip
    ```

5. Utwórz katalog w magazynie usługi HDInsight, a następnie skopiuj plik CSV do katalogu, wprowadzając poniższe polecenie:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Przekształcanie danych za pomocą zapytania oprogramowania Hive

Istnieje wiele sposobów uruchamiania zadania oprogramowania Hive w klastrze usługi HDInsight. W tej sekcji uruchomisz zadanie oprogramowania Hive za pomocą usługi [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell). Aby uzyskać informacje o innych metodach uruchamiania zadania Hive, zobacz [Używanie oprogramowania Apache Hive w usłudze HDInsight](../hadoop/hdinsight-use-hive.md).

W ramach zadania oprogramowania Hive można zaimportować dane z pliku csv do tabeli oprogramowania Hive o nazwie **Delays**.

1. Z poziomu monitu SSH, który istnieje już dla klastra usługi HDInsight, użyj następującego polecenia, aby utworzyć i edytować nowy plik o nazwie **flightdelays. HQL**:

    ```bash
    nano flightdelays.hql
    ```

2. Użyj następującego tekstu jako zawartości tego pliku:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Aby zapisać plik, naciśnij **klawisze Ctrl + X**, a następnie **y**, a następnie wprowadź.

4. Aby uruchomić oprogramowanie Hive i uruchomić plik **flightdelays.hql**, użyj następującego polecenia:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Gdy skrypt **flightdelays.hql** zakończy działanie, użyj następującego polecenia, aby otworzyć interaktywną sesję usługi Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Po otrzymaniu wiersza `jdbc:hive2://localhost:10001/>` użyj następującego zapytania, aby pobrać dane z zaimportowanych danych opóźnień lotów:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    To zapytanie pobiera listę miejscowości z opóźnieniami ze względu na pogodę i średni czas opóźnienia, a następnie zapisuje ją w lokalizacji `/tutorials/flightdelays/output`. Później narzędzie Sqoop odczyta dane z tej lokalizacji i wyeksportuje je do bazy danych Azure SQL Database.

7. Aby zakończyć działanie usługi Beeline, wprowadź polecenie `!quit` w wierszu polecenia.

## <a name="create-a-sql-database-table"></a>Tworzenie tabeli bazy danych SQL

Istnieje wiele sposobów nawiązywania połączenia z bazą danych SQL i tworzenia tabeli. W poniższej procedurze użyto rozwiązania [FreeTDS](http://www.freetds.org/) z klastra usługi HDInsight.

1. Aby zainstalować FreeTDS, użyj następującego polecenia z otwartego połączenia SSH z klastrem:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Po zakończeniu instalacji użyj następującego polecenia, aby nawiązać połączenie z usługą SQL Database.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Uzyskasz dane wyjściowe podobne do następującego tekstu:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. W wierszu `1>` wprowadź następujące wiersze:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Jeśli wprowadzono instrukcję `GO`, zostaną obliczone poprzednie instrukcje. Ta instrukcja tworzy tabelę o nazwie **opóźnienia** z indeksem klastrowanym.

    Użyj następującego zapytania, aby sprawdzić, czy utworzono tabelę:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    Dane wyjściowe będą podobne do następującego tekstu:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Wprowadź ciąg `exit` w wierszu `1>`, aby zakończyć działanie narzędzia tsql.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Eksportowanie danych do SQL Database przy użyciu oprogramowania Apache Sqoop

W poprzednich sekcjach skopiowano przekształcone dane w lokalizacji `/tutorials/flightdelays/output`. W tej sekcji użyto Sqoop do wyeksportowania danych z `/tutorials/flightdelays/output` do tabeli utworzonej w Azure SQL Database.

1. Sprawdź, czy Sqoop może zobaczyć swoją bazę danych SQL, wprowadzając następujące polecenie:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    To polecenie zwraca listę baz danych, w tym bazę danych, w której utworzono `delays` wcześniej tabelę.

2. Wyeksportuj dane z `/tutorials/flightdelays/output` do `delays` tabeli, wprowadzając następujące polecenie:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop nawiązuje połączenie z bazą danych, która zawiera `delays` tabelę, i eksportuje dane z `/tutorials/flightdelays/output` katalogu do `delays` tabeli.

3. Po zakończeniu wykonywania polecenia Sqoop Użyj narzędzia TSQL, aby nawiązać połączenie z bazą danych, wprowadzając następujące polecenie:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Aby sprawdzić, czy dane zostały wyeksportowane do tabeli delays, użyj następujących instrukcji:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Powinna zostać wyświetlona lista danych w tabeli. Tabela zawiera nazwę miejscowości i średni czas opóźnienia lotów dla tej miejscowości.

    Wpisz `exit`, aby zakończyć działanie narzędzia tsql.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu korzystania z samouczka warto usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłata jest naliczana również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używana. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku trwały plik danych CSV został zaimportowany do magazynu klastra usługi HDInsight, a następnie przekształcony dane przy użyciu interakcyjnego zapytania w usłudze Azure HDInsight.  Przejdź do następnego samouczka, aby dowiedzieć się więcej o łączniku magazynu Apache Hive.

> [!div class="nextstepaction"]
> [Integrowanie Apache Spark i Apache Hive z łącznikiem magazynu Hive](./apache-hive-warehouse-connector.md)
