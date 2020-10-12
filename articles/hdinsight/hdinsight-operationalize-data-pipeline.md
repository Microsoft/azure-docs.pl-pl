---
title: Operacjonalizować potoku analizy danych — Azure
description: Konfigurowanie i uruchamianie przykładowego potoku danych, który jest wyzwalany przez nowe dane i tworzy zwięzłe wyniki.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: 1e73c403a03eef9a47bc0550b37769db302a599c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504422"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operacjonalizacja potoku analizy danych

*Potoki danych* w ramach wielu rozwiązań do analizy danych. Jak sugeruje nazwa, Potok danych wykonuje pierwotne dane, czyści i przekształca je w razie potrzeby, a następnie zazwyczaj wykonuje obliczenia lub agregacje przed zapisaniem przetworzonych danych. Przetworzone dane są używane przez klientów, raporty lub interfejsy API. Potok danych musi zapewniać powtarzające się wyniki, zarówno zgodnie z harmonogramem, jak i wyzwalane przez nowe dane.

W tym artykule opisano, jak operacjonalizować potoki danych w celu ich powtarzalności przy użyciu Oozie uruchomionych w klastrach usługi HDInsight Hadoop. Przykładowy scenariusz przeprowadzi Cię przez potok danych, który przygotowuje i przetwarza dane z szeregów czasowych lotów lotniczych.

W poniższym scenariuszu dane wejściowe są plikiem prostym zawierającym partię danych lotu przez jeden miesiąc. Te dane lotu obejmują takie informacje, jak źródło i docelowy port lotniczy, kilometry, godziny wyjazdu i przybycia itd. Celem tego potoku jest podsumowanie dziennej wydajności linii lotniczych, gdzie każdy z nich ma jeden wiersz dla każdego dnia z średnimi opóźnieniami wyjazdu i przybycia w ciągu kilku minut, a całkowita liczba kilometrów jest w tym dniu.

| YEAR | MONTH | DAY_OF_MONTH | KARUZEL |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10,142229 | 7,862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9,435449 | 5,482143 | 572289 |
| 2017 | 1 | 3 | LISTY DYSTRYBUCYJNEJ | 6,935409 | -2,1893024 | 1909696 |

Przykładowy potok czeka na nadejście nowych danych lotu okresu, a następnie przechowuje te szczegółowe informacje o locie do magazynu danych Apache Hive na potrzeby analiz długoterminowych. Potok tworzy również znacznie mniejszy zestaw danych, który podsumowuje tylko dzienne dane lotu. Te codzienne dane podsumowania lotu są wysyłane do SQL Database w celu udostępnienia raportów, takich jak witryna sieci Web.

Poniższy diagram ilustruje przykład potoku.

![Przykładowy Potok danych przykładu lotu HDI](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Omówienie rozwiązania Apache Oozie

Ten potok używa platformy Apache Oozie uruchomionej w klastrze usługi HDInsight Hadoop.

Oozie opisuje swoje potoki pod względem *akcji*, *przepływów pracy*i *koordynatorów*. Akcje określają rzeczywistą służbę do wykonania, na przykład uruchomienie zapytania programu Hive. Przepływy pracy definiują sekwencję akcji. Koordynatorzy definiują harmonogram wykonywania przepływu pracy. Koordynatorzy mogą również oczekiwać na dostępność nowych danych przed uruchomieniem wystąpienia przepływu pracy.

Na poniższym diagramie przedstawiono ogólny projekt tego przykładowego potoku Oozie.

![Potok danych przykładowego lotu Oozie](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Udostępnianie zasobów platformy Azure

Ten potok wymaga Azure SQL Database i klastra usługi HDInsight Hadoop w tej samej lokalizacji. Azure SQL Database przechowuje zarówno dane podsumowania generowane przez potok, jak i magazyn metadanych Oozie.

### <a name="provision-azure-sql-database"></a>Azure SQL Database udostępniania

1. Utwórz Azure SQL Database. Zobacz [tworzenie Azure SQL Database w Azure Portal](../azure-sql/database/single-database-create-quickstart.md).

1. Aby upewnić się, że klaster usługi HDInsight może uzyskać dostęp do połączonej Azure SQL Database, skonfiguruj Azure SQL Database reguły zapory, aby umożliwić usługom i zasobom platformy Azure dostęp do serwera. Tę opcję można włączyć w Azure Portal, wybierając pozycję **Ustaw zaporę serwera**i wybierając pozycję **włączone w** obszarze **Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera** dla Azure SQL Database. Aby uzyskać więcej informacji, zobacz [Tworzenie reguł zapory IP i zarządzanie nimi](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

1. Za pomocą [edytora zapytań](../azure-sql/database/single-database-create-quickstart.md#query-the-database) wykonaj następujące instrukcje SQL, aby utworzyć `dailyflights` tabelę, w której będą przechowywane podsumowane dane z każdego przebiegu potoku.

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

Azure SQL Database jest teraz gotowa.

### <a name="provision-an-apache-hadoop-cluster"></a>Inicjowanie obsługi klastra Apache Hadoop

Utwórz klaster Apache Hadoop z niestandardowym magazynem metadanych. Podczas tworzenia klastra z poziomu portalu z karty **Magazyn** upewnij się, że wybrano SQL Database w obszarze **ustawienia magazynu metadanych**. Aby uzyskać więcej informacji na temat wybierania magazynu metadanych, zobacz [Wybieranie niestandardowego magazynu metadanych podczas tworzenia klastra](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Aby uzyskać więcej informacji na temat tworzenia klastrów, zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>Weryfikowanie konfiguracji tunelowania SSH

Aby użyć konsoli sieci Web Oozie do wyświetlania stanu koordynatora i wystąpień przepływu pracy, należy skonfigurować tunel SSH do klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [tunel SSH](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Możesz również użyć programu Chrome z rozszerzeniem [serwera proxy Foxy](https://getfoxyproxy.org/) , aby przeglądać zasoby sieci Web klastra za pośrednictwem tunelu SSH. Skonfiguruj je do serwera proxy wszystkie żądania za pomocą hosta `localhost` na porcie 9876 tunelu. Takie podejście jest zgodne z podsystemem Windows dla systemu Linux, znanym również jako bash w systemie Windows 10.

1. Uruchom następujące polecenie, aby otworzyć tunel SSH do klastra, gdzie `CLUSTERNAME` jest nazwą klastra:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Sprawdź, czy tunel działa, przechodząc do Ambari w węźle głównym, przeglądając do:

    `http://headnodehost:8080`

1. Aby uzyskać dostęp do **konsoli sieci Web Oozie** z poziomu usługi Ambari, przejdź do strony **Oozie**  >  **szybkie linki** > [Active Server] > **Oozie interfejs użytkownika sieci Web**.

## <a name="configure-hive"></a>Konfigurowanie programu Hive

### <a name="upload-data"></a>Przekazywanie danych

1. Pobierz przykładowy plik CSV zawierający dane lotu przez jeden miesiąc. Pobierz plik ZIP `2017-01-FlightData.zip` z [repozytorium GitHub usługi HDInsight](https://github.com/hdinsight/hdinsight-dev-guide) i rozpakuj go do pliku CSV `2017-01-FlightData.csv` .

1. Skopiuj ten plik CSV do konta magazynu platformy Azure dołączonego do klastra usługi HDInsight i umieść go w `/example/data/flights` folderze.

    1. Użyj punktu połączenia usługi, aby skopiować pliki z komputera lokalnego do lokalnego magazynu węzła głównego klastra usługi HDInsight.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Użyj [polecenia SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując je `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. Z poziomu sesji SSH Użyj polecenia HDFS, aby skopiować plik z lokalnego magazynu węzła głównego do usługi Azure Storage.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Tworzenie tabel

Przykładowe dane są teraz dostępne. Jednak potok wymaga dwóch tabel programu Hive do przetwarzania, jeden dla danych przychodzących ( `rawFlights` ) i jeden dla danych podsumowanych ( `flights` ). Utwórz te tabele w Ambari w następujący sposób.

1. Zaloguj się do Ambari, przechodząc do `http://headnodehost:8080` .

2. Z listy usług wybierz pozycję **Hive**.

    ![Lista usług Apache Ambari Services — Wybieranie gałęzi](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Wybierz pozycję **Przejdź do widoku** obok etykiety widok programu Hive 2,0.

    ![Lista podsumowania Apache Hive Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. W obszarze tekstu zapytania Wklej następujące instrukcje, aby utworzyć `rawFlights` tabelę. `rawFlights`Tabela zawiera schemat na potrzeby odczytywania plików CSV znajdujących się w `/example/data/flights` folderze w usłudze Azure Storage.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. Wybierz pozycję **Execute (wykonaj** ), aby utworzyć tabelę.

    ![zapytanie Hive usługi HDI Ambari Services](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Aby utworzyć `flights` tabelę, Zastąp tekst w obszarze tekstu zapytania poniższymi instrukcjami. `flights`Tabela jest tabelą zarządzaną przez program Hive służącą do partycjonowania danych załadowanych do nich przez rok, miesiąc i dzień miesiąca. Ta tabela będzie zawierać wszystkie historyczne dane lotu z najniższym rozdrobnieniem obecnym w danych źródłowych jednego wiersza na lot.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. Wybierz pozycję **Execute (wykonaj** ), aby utworzyć tabelę.

## <a name="create-the-oozie-workflow"></a>Tworzenie przepływu pracy Oozie

Potoki zwykle przetwarzają dane w partiach według danego przedziału czasu. W takim przypadku potok codziennie przetwarza dane lotu. Takie podejście umożliwia codzienne, cotygodniowe, comiesięczne lub roczne wprowadzanie wejściowych plików CSV.

Przykładowy przepływ pracy przetwarza dziennie danych lotu w trzech głównych krokach:

1. Uruchom zapytanie programu Hive, aby wyodrębnić dane z zakresu dat tego dnia ze źródłowego pliku CSV reprezentowanego przez `rawFlights` tabelę i wstawić dane do `flights` tabeli.
2. Uruchom zapytanie programu Hive, aby dynamicznie utworzyć tabelę przejściową w ramach platformy Hive na dzień, która zawiera kopię danych lotu podsumowaną według dnia i operatora.
3. Za pomocą platformy Apache Sqoop Skopiuj wszystkie dane z codziennej tabeli przemieszczania do `dailyflights` tabeli docelowej w Azure SQL Database. Sqoop odczytuje wiersze źródłowe z danych znajdujących się w tabeli Hive znajdujących się w usłudze Azure Storage i ładuje je do SQL Database przy użyciu połączenia JDBC.

Te trzy kroki są koordynowane przez przepływ pracy Oozie.

1. Na lokalnej stacji roboczej Utwórz plik o nazwie `job.properties` . Użyj poniższego tekstu jako początkowej zawartości dla tego pliku.
Następnie zaktualizuj wartości dla określonego środowiska. Tabela poniżej tekstu podsumowuje poszczególne właściwości i wskazuje, gdzie można znaleźć wartości dla własnego środowiska.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Właściwość | Źródło wartości |
    | --- | --- |
    | nameNode | Pełna ścieżka do kontenera magazynu platformy Azure dołączonego do klastra usługi HDInsight. |
    | jobTracker | Wewnętrzna nazwa hosta do węzła głównego PRZĘDZy w klastrze. Na stronie głównej Ambari wybierz z listy usług pozycję PRZĘDZa, a następnie wybierz pozycję Active Menedżer zasobów. Identyfikator URI nazwy hosta jest wyświetlany w górnej części strony. Dołącz port 8050. |
    | Zmienną QueueName | Nazwa kolejki PRZĘDZy użyta podczas planowania akcji programu Hive. Pozostaw jako domyślny. |
    | oozie.use.system. LIBPATH | Pozostaw wartość true. |
    | appBase | Ścieżka do podfolderu w usłudze Azure Storage, w której jest wdrażany przepływ pracy Oozie i pliki pomocnicze. |
    | Oozie. WF. Application. Path | Lokalizacja przepływu pracy Oozie `workflow.xml` do uruchomienia. |
    | hiveScriptLoadPartition | Ścieżka do pliku zapytania programu Hive w usłudze Azure Storage `hive-load-flights-partition.hql` . |
    | hiveScriptCreateDailyTable | Ścieżka do pliku zapytania programu Hive w usłudze Azure Storage `hive-create-daily-summary-table.hql` . |
    | hiveDailyTableName | Generowana dynamicznie nazwa do użycia w tabeli przemieszczania. |
    | hiveDataFolder | Ścieżka w usłudze Azure Storage do danych zawartych w tabeli przemieszczania. |
    | sqlDatabaseConnectionString | Parametry połączenia składni JDBC z Azure SQL Database. |
    | sqlDatabaseTableName | Nazwa tabeli w Azure SQL Database, do której są wstawiane wiersze podsumowania. Pozostaw jako `dailyflights` . |
    | rok | Składnik roku dnia, dla którego są obliczane podsumowania lotu. Pozostaw jako is. |
    | miesiąc | Składnik miesiąca dnia, dla którego są obliczane podsumowania lotu. Pozostaw jako is. |
    | dzień | Składnik dzień miesiąca dnia, dla którego są obliczane podsumowania lotu. Pozostaw jako is. |

1. Na lokalnej stacji roboczej Utwórz plik o nazwie `hive-load-flights-partition.hql` . Użyj poniższego kodu jako zawartości pliku.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
        FL_DATE,
        CARRIER,
        FL_NUM,
        ORIGIN,
        DEST,
        DEP_DELAY,
        ARR_DELAY,
        ACTUAL_ELAPSED_TIME,
        DISTANCE,
        YEAR,
        MONTH,
        DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Zmienne Oozie używają składni `${variableName}` . Te zmienne są ustawiane w `job.properties` pliku. Oozie podstawia rzeczywiste wartości w czasie wykonywania.

1. Na lokalnej stacji roboczej Utwórz plik o nazwie `hive-create-daily-summary-table.hql` . Użyj poniższego kodu jako zawartości pliku.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER STRING,
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    To zapytanie tworzy tabelę przejściową, w której będą przechowywane tylko podsumowane dane przez jeden dzień. należy zwrócić uwagę na instrukcję SELECT, która oblicza średnie opóźnienia i łączną odległość przewidzianą przez operatora według dnia. Dane wstawione do tej tabeli przechowywane we wskazanej lokalizacji (ścieżki wskazanej przez zmienną hiveDataFolder), aby można było ich użyć jako źródła dla Sqoop w następnym kroku.

1. Na lokalnej stacji roboczej Utwórz plik o nazwie `workflow.xml` . Użyj poniższego kodu jako zawartości pliku. Powyższe kroki są wyrażane jako osobne akcje w pliku przepływu pracy Oozie.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

Dwie zapytania programu Hive są dostępne przez ich ścieżkę w usłudze Azure Storage, a pozostałe wartości zmiennych są dostarczane przez `job.properties` plik. Ten plik umożliwia skonfigurowanie przepływu pracy do uruchomienia w dniu 3 stycznia 2017.

## <a name="deploy-and-run-the-oozie-workflow"></a>Wdrażanie i uruchamianie przepływu pracy Oozie

Użyj punktu połączenia usługi z sesji bash, aby wdrożyć przepływ pracy Oozie ( `workflow.xml` ), zapytania programu Hive ( `hive-load-flights-partition.hql` i `hive-create-daily-summary-table.hql` ) oraz konfigurację zadania ( `job.properties` ).  W programie Oozie tylko `job.properties` plik może istnieć w lokalnym magazynie węzła głównego. Wszystkie inne pliki muszą być przechowywane w systemie plików HDFS, w tym przypadku usługi Azure Storage. Akcja Sqoop używana przez przepływ pracy zależy od sterownika JDBC do komunikowania się z SQL Database, który należy skopiować z węzła głównego do systemu plików HDFS.

1. Utwórz `load_flights_by_day` podfolder pod ścieżką użytkownika w lokalnym magazynie węzła głównego. Z otwartej sesji SSH wykonaj następujące polecenie:

    ```bash
    mkdir load_flights_by_day
    ```

1. Skopiuj wszystkie pliki w bieżącym katalogu ( `workflow.xml` `job.properties` pliki i) do `load_flights_by_day` podfolderu. Na lokalnej stacji roboczej wykonaj następujące polecenie:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Skopiuj pliki przepływu pracy do systemu plików HDFS. Z otwartej sesji SSH wykonaj następujące polecenia:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Skopiuj `mssql-jdbc-7.0.0.jre8.jar` z lokalnego węzła głównego do folderu przepływu pracy w systemie HDFS. Popraw polecenie stosownie do potrzeb, Jeśli klaster zawiera inny plik JAR. Popraw `workflow.xml` w razie potrzeby, aby odzwierciedlić inny plik JAR. Z otwartej sesji SSH wykonaj następujące polecenie:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Uruchom przepływ pracy. Z otwartej sesji SSH wykonaj następujące polecenie:

    ```bash
    oozie job -config job.properties -run
    ```

1. Obserwuj stan przy użyciu konsoli sieci Web Oozie. W programie Ambari wybierz pozycję **Oozie**, **szybkie linki**i **Oozie konsolę sieci Web**. Na karcie **zadania przepływu pracy** wybierz pozycję **wszystkie zadania**.

    ![przepływy pracy konsoli sieci Web HDI Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. Gdy stan ma wartość powodzenie, wykonaj zapytanie do tabeli SQL Database w celu wyświetlenia wstawionych wierszy. Korzystając z Azure Portal, przejdź do okienka dla SQL Database, wybierz pozycję **Narzędzia**, a następnie otwórz **Edytor zapytań**.

    ```sql
    SELECT * FROM dailyflights
    ```

Teraz, gdy przepływ pracy jest uruchomiony dla pojedynczego dnia testowego, możesz otoczyć ten przepływ pracy koordynatorem, który planuje przepływ pracy tak, aby był uruchamiany codziennie.

## <a name="run-the-workflow-with-a-coordinator"></a>Uruchamianie przepływu pracy z koordynatorem

Aby zaplanować ten przepływ pracy w taki sposób, aby był uruchamiany codziennie (lub wszystkie dni w zakresie dat), można użyć koordynatora. Koordynator jest zdefiniowany przez plik XML, na przykład `coordinator.xml` :

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Jak widać, większość koordynatora przekazuje tylko informacje o konfiguracji do wystąpienia przepływu pracy. Istnieje jednak kilka ważnych elementów, które należy wywołać.

* Punkt 1: `start` atrybuty i `end` w `coordinator-app` samym elemencie kontrolują przedział czasu, w którym działa koordynator.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordynator jest odpowiedzialny za planowanie akcji w ramach `start` i `end` zakres dat, zgodnie z interwałem określonym przez `frequency` atrybut. Każda zaplanowana akcja z kolei uruchamia przepływ pracy zgodnie z konfiguracją. W powyższej definicji koordynatora koordynator jest skonfigurowany do uruchamiania akcji od 1 stycznia 2017 do 5 stycznia 2017. Częstotliwość jest ustawiana na jeden dzień przez wyrażenie częstotliwości [języka wyrażenia Oozie](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) `${coord:days(1)}` . Powoduje to, że koordynator zaplanował akcję (a tym samym przepływ pracy) raz dziennie. W przypadku zakresów dat, które znajdują się w przeszłości, jak w tym przykładzie, akcja zostanie zaplanowana do uruchomienia bez opóźnień. Początek daty, od której zaplanowano uruchomienie akcji, nazywa się *czasem nominalnym*. Na przykład w celu przetworzenia danych od 1 stycznia 2017 koordynator będzie planować akcję o nominalnym czasie wynoszącym 2017-01-01T00:00:00 GMT.

* Punkt 2: w zakresie dat przepływu pracy `dataset` element określa, gdzie ma być wyszukiwane w systemie plików HDFS dla danych określonego zakresu dat, i określa, jak Oozie określa, czy dane są jeszcze dostępne do przetworzenia.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Ścieżka do danych w systemie plików HDFS jest tworzona dynamicznie zgodnie z wyrażeniem podanym w `uri-template` elemencie. W tym koordynatorze jest również używana częstotliwość o jeden dzień z zestawem danych. W czasie, gdy akcje są zaplanowane (i definiuje ich czasy nominalne), `initial-instance` i `frequency` na zestawie danych sterują obliczaniem daty, która jest używana w konstruowaniu `uri-template` . W takim przypadku należy ustawić początkowe wystąpienie na jeden dzień przed rozpoczęciem koordynatora, aby upewnić się, że wybiera on pierwszy dzień (1/1/2017) danych. Obliczenia daty zestawu danych są przenoszone z wartości `initial-instance` (12/31/2016) w przyrostach częstotliwości zestawu danych (jeden dzień) do momentu znalezienia najnowszej daty, która nie przekazuje czasu nominalnego ustawionego przez koordynatora (2017-01-01T00:00:00 GMT dla pierwszej akcji).

    Pusty `done-flag` element wskazuje, że gdy Oozie sprawdza obecność danych wejściowych w wyznaczonym czasie, Oozie określa dane, czy są dostępne według obecności katalogu lub pliku. W takim przypadku jest to obecność pliku CSV. Jeśli plik CSV jest obecny, Oozie zakłada, że dane są gotowe i uruchamia wystąpienie przepływu pracy w celu przetworzenia pliku. Jeśli nie ma pliku CSV, Oozie zakłada, że dane nie są jeszcze gotowe i że przebieg przepływu pracy przechodzi w stan oczekiwania.

* Punkt 3: `data-in` element określa określoną sygnaturę czasową, która ma być używana jako wartość nominalna podczas zamieniania wartości w `uri-template` dla skojarzonego zestawu danych.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    W takim przypadku należy ustawić wystąpienie na wyrażenie `${coord:current(0)}` , które jest tłumaczone na użycie nominalnego czasu akcji zgodnie z oryginalnie zaplanowaną przez koordynatora. Innymi słowy, gdy koordynator planuje uruchomienie akcji z czasem nominalnym 01/01/2017, a następnie 01/01/2017 jest używany do zastępowania zmiennych YEAR (2017) i miesiąca (01) w szablonie identyfikatora URI. Gdy szablon URI zostanie obliczony dla tego wystąpienia, Oozie sprawdza, czy oczekiwany katalog lub plik jest dostępny i harmonogram następnego przebiegu przepływu pracy.

Trzy poprzednie punkty łączą się, aby dać sytuację, w której koordynator planuje przetwarzanie danych źródłowych w codziennym miejscu.

* Punkt 1: Koordynator rozpoczyna się od nominalnej daty 2017-01-01.

* Punkt 2: Oozie szuka danych dostępnych w `sourceDataFolder/2017-01-FlightData.csv` .

* Punkt 3: gdy Oozie odnajdzie ten plik, planuje wystąpienie przepływu pracy, który będzie przetwarzać dane dla 2017-01-01. Oozie następnie kontynuuje przetwarzanie dla 2017-01-02. Ta ocena powtarza się do, ale nie do 2017-01-05.

Podobnie jak w przypadku przepływów pracy konfiguracja koordynatora jest definiowana w `job.properties` pliku, który ma nadzbiór ustawień używanych przez przepływ pracy.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

Jedyne nowe właściwości wprowadzone w tym `job.properties` pliku to:

| Właściwość | Źródło wartości |
| --- | --- |
| Oozie. coord. Application. Path | Wskazuje lokalizację `coordinator.xml` pliku zawierającego koordynatora Oozie do uruchomienia. |
| hiveDailyTableNamePrefix | Prefiks używany podczas dynamicznego tworzenia nazwy tabeli dla tabeli przemieszczania. |
| hiveDataFolderPrefix | Prefiks ścieżki, w której będą przechowywane wszystkie tabele przemieszczania. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Wdrażanie i uruchamianie koordynatora Oozie

Aby uruchomić potok z koordynatorem, Kontynuuj w podobny sposób jak w przypadku przepływu pracy, z wyjątkiem tego, że pracujesz z folderu o jeden poziom powyżej folderu zawierającego przepływ pracy. Ta konwencja folderu oddziela koordynatorów z przepływów pracy na dysku, dzięki czemu można skojarzyć jednego koordynatora z różnymi podrzędnymi przepływami pracy.

1. Użyj punktu połączenia usługi z komputera lokalnego, aby skopiować pliki koordynatora do lokalnego magazynu węzła głównego klastra.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. Użyj protokołu SSH do węzła głównego.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Skopiuj pliki koordynatora do systemu plików HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Uruchom koordynatora.

    ```bash
    oozie job -config job.properties -run
    ```

5. Sprawdź stan za pomocą konsoli sieci Web Oozie, tym razem wybierając kartę **zadania koordynatora** , a następnie  **wszystkie zadania**.

    ![Zadania koordynatora konsoli sieci Web Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Wybierz wystąpienie koordynatora, aby wyświetlić listę zaplanowanych akcji. W takim przypadku powinny być widoczne cztery działania z przedziałami nominalnymi z zakresu od 1/1/2017 do 1/4/2017.

    ![Zadanie koordynatora konsoli sieci Web Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Każda akcja na tej liście odpowiada wystąpieniu przepływu pracy, który przetwarza dane z jednego dnia, gdzie początek tego dnia jest wskazywany przez nominalny czas.

## <a name="next-steps"></a>Następne kroki

[Dokumentacja oprogramowania Apache Oozie](https://oozie.apache.org/docs/4.2.0/index.html)
