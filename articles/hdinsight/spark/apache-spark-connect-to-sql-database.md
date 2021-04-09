---
title: Użyj Apache Spark, aby odczytywać i zapisywać dane w Azure SQL Database
description: Dowiedz się, jak skonfigurować połączenie między klastrem usługi HDInsight Spark a Azure SQL Database. Aby odczytywać dane, zapisywać dane i przesyłać strumieniowo dane do bazy danych SQL
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 0c9c3b2d915e54cf954703c56c2087637cc80aa0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864622"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Korzystanie z klastra Spark w usłudze HDInsight w celu odczytywania i zapisywania danych w Azure SQL Database

Dowiedz się, jak połączyć Klaster Apache Spark w usłudze Azure HDInsight z Azure SQL Database. Następnie Odczytuj, zapisuj i przesyłaj strumieniowo dane do bazy danych SQL. Instrukcje w tym artykule używają Jupyter Notebook do uruchamiania fragmentów kodu Scala. Można jednak utworzyć autonomiczną aplikację w Scala lub Python i wykonać te same zadania.

## <a name="prerequisites"></a>Wymagania wstępne

* Azure HDInsight Spark klaster.  Postępuj zgodnie z instrukcjami w temacie [Tworzenie klastra Apache Spark w usłudze HDInsight](apache-spark-jupyter-spark-sql.md).

* Azure SQL Database. Postępuj zgodnie z instrukcjami w temacie [Tworzenie bazy danych w Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md). Upewnij się, że tworzysz bazę danych z przykładowym schematem **AdventureWorksLT** i danymi. Upewnij się również, że utworzono regułę zapory na poziomie serwera, aby umożliwić adres IP klienta dostępu do bazy danych SQL. Instrukcje dotyczące dodawania reguły zapory są dostępne w tym samym artykule. Po utworzeniu bazy danych SQL upewnij się, że następujące wartości są odpowiednie. Są one potrzebne do łączenia się z bazą danych z klastra Spark.

    * Nazwa serwera.
    * Nazwa bazy danych.
    * Azure SQL Database nazwę użytkownika/hasło administratora.

* SQL Server Management Studio (SSMS). Postępuj zgodnie z instrukcjami wyświetlanymi w [programie SSMS, aby nawiązywać połączenia i wykonywać zapytania dotyczące danych](../../azure-sql/database/connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

Zacznij od utworzenia Jupyter Notebook skojarzonej z klastrem Spark. Ten Notes służy do uruchamiania fragmentów kodu używanych w tym artykule.

1. W [Azure Portal](https://portal.azure.com/)Otwórz klaster.
1. Wybierz **Jupyter Notebook** pod **pulpity nawigacyjne klastra** po prawej stronie.  Jeśli nie widzisz **pulpitów nawigacyjnych klastra**, wybierz pozycję **Przegląd** z menu po lewej stronie. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png " alt-text="Jupyter Notebook na Apache Spark" border="true":::

   > [!NOTE]  
   > Możesz również uzyskać dostęp do Jupyter Notebook w klastrze Spark, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. W Jupyter Notebook w prawym górnym rogu kliknij pozycję **Nowy**, a następnie kliknij pozycję **Spark** , aby utworzyć Notes Scala. Notesy Jupyter w klastrze usługi HDInsight Spark udostępniają również jądro **PySpark** dla aplikacji python2 oraz jądra **PySpark3** dla aplikacji python3. W tym artykule utworzymy Notes Scala.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png " alt-text="Jądra dla Jupyter Notebook na platformie Spark" border="true":::

    Aby uzyskać więcej informacji na temat jądra, zobacz [używanie Jupyter Notebook jądra z klastrami Apache Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > W tym artykule używamy jądra Spark (Scala), ponieważ dane przesyłane strumieniowo z platformy Spark do SQL Database są obecnie obsługiwane tylko w Scala i Java. Mimo że odczytywanie i zapisywanie w języku SQL można wykonać przy użyciu języka Python, aby zapewnić spójność w tym artykule, firma Microsoft używa Scala dla wszystkich trzech operacji.

1. Zostanie otwarty nowy Notes z domyślną nazwą, **bez tytułu**. Kliknij nazwę notesu i wprowadź wybraną nazwę.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png " alt-text="Wprowadzanie nazwy notesu" border="true":::

Teraz możesz rozpocząć tworzenie aplikacji.

## <a name="read-data-from-azure-sql-database"></a>Odczytaj dane z Azure SQL Database

W tej sekcji odczytujesz dane z tabeli (na przykład **tabeli SalesLT. Address**), która istnieje w bazie danych AdventureWorks.

1. W nowej Jupyter Notebook w komórce kodu wklej poniższy fragment kodu i Zastąp wartości symboli zastępczych wartościami dla bazy danych.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Naciśnij klawisze **SHIFT + ENTER**, aby uruchomić komórkę kodu.  

1. Użyj poniższego fragmentu, aby utworzyć adres URL JDBC, który można przekazać do interfejsów API Spark Dataframe. Kod tworzy `Properties` obiekt do przechowywania parametrów. Wklej ten fragment w komórce kodu i naciśnij klawisze **SHIFT + ENTER** , aby uruchomić.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Skorzystaj z poniższego fragmentu, aby utworzyć ramkę danych zawierającą dane z tabeli w bazie danych. W tym fragmencie kodu używamy `SalesLT.Address` tabeli, która jest dostępna w ramach bazy danych **AdventureWorksLT** . Wklej ten fragment w komórce kodu i naciśnij klawisze **SHIFT + ENTER** , aby uruchomić.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Teraz można wykonywać operacje na ramce Dataframe, na przykład pobierając schemat danych:

    ```scala
    sqlTableDF.printSchema
    ```

    Zobaczysz dane wyjściowe podobne do poniższej ilustracji:

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png " alt-text="dane wyjściowe schematu" border="true":::

1. Można również wykonywać operacje podobne do, pobierając 10 pierwszych wierszy.

    ```scala
    sqlTableDF.show(10)
    ```

1. Lub Pobierz określone kolumny z zestawu danych.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Zapisywanie danych w Azure SQL Database

W tej sekcji używamy przykładowego pliku CSV dostępnego w klastrze do utworzenia tabeli w bazie danych i wypełnienia jej danymi. Przykładowy plik CSV (**HVAC.csv**) jest dostępny we wszystkich klastrach usługi HDInsight w lokalizacji `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` .

1. W nowej Jupyter Notebook w komórce kodu wklej poniższy fragment kodu i Zastąp wartości symboli zastępczych wartościami dla bazy danych.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Naciśnij klawisze **SHIFT + ENTER**, aby uruchomić komórkę kodu.  

1. Poniższy fragment kodu kompiluje adres URL JDBC, który można przekazać do interfejsów API Spark Dataframe. Kod tworzy `Properties` obiekt do przechowywania parametrów. Wklej ten fragment w komórce kodu i naciśnij klawisze **SHIFT + ENTER** , aby uruchomić.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Poniższy fragment kodu służy do wyodrębniania schematu danych w HVAC.csv i używania schematu do ładowania danych z woluminu CSV w ramce Dataframe `readDf` . Wklej ten fragment w komórce kodu i naciśnij klawisze **SHIFT + ENTER** , aby uruchomić.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Użyj `readDf` ramki Dataframe, aby utworzyć tabelę tymczasową `temphvactable` . Następnie użyj tabeli tymczasowej, aby utworzyć tabelę programu Hive `hvactable_hive` .

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Na koniec Użyj tabeli Hive, aby utworzyć tabelę w bazie danych. Poniższy fragment kodu tworzy `hvactable` się w Azure SQL Database.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Połącz się z Azure SQL Database przy użyciu programu SSMS i sprawdź, czy `dbo.hvactable` tam widzisz.

    a. Uruchom program SSMS i Połącz się z Azure SQL Database, podając szczegóły połączenia, jak pokazano na poniższym zrzucie ekranu.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png " alt-text="Nawiązywanie połączenia z SQL Database przy użyciu SSMS1" border="true":::

    b. W obszarze **Eksplorator obiektów** rozwiń bazę danych i węzeł tabeli, aby wyświetlić utworzony obiekt **dbo. HVAC** .

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png " alt-text="Nawiązywanie połączenia z SQL Database przy użyciu SSMS2" border="true":::

1. Uruchom zapytanie w programie SSMS, aby zobaczyć kolumny w tabeli.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Przesyłanie strumieniowe danych do Azure SQL Database

W tej sekcji przesyłamy strumieniowo dane do programu `hvactable` , który został utworzony w poprzedniej sekcji.

1. W pierwszym kroku upewnij się, że nie ma żadnych rekordów w `hvactable` . Za pomocą programu SSMS Uruchom następujące zapytanie w tabeli.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Utwórz nowy Jupyter Notebook w klastrze usługi HDInsight Spark. W komórce kodu wklej poniższy fragment kodu, a następnie naciśnij klawisze **SHIFT + ENTER**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Przesyłamy strumieniowo dane z **HVAC.csv** do `hvactable` . Plik HVAC.csv jest dostępny w klastrze pod adresem `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . W poniższym fragmencie kodu najpierw uzyskasz schemat danych do przesyłania strumieniowego. Następnie tworzymy ramkę danych przesyłania strumieniowego przy użyciu tego schematu. Wklej ten fragment w komórce kodu i naciśnij klawisze **SHIFT + ENTER** , aby uruchomić.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. Dane wyjściowe pokazują schemat **HVAC.csv**. `hvactable`Ma również ten sam schemat. W danych wyjściowych znajdują się kolumny w tabeli.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png " alt-text="&quot;Apache Spark tabeli schematu usługi HDInsight&quot;" border="true":::

1. Na koniec użyj poniższego fragmentu kodu, aby odczytać dane z HVAC.csv i przesłać je strumieniowo do `hvactable` bazy danych programu. Wklej ten fragment w komórce kodu, Zastąp wartości symboli zastępczych wartościami dla bazy danych, a następnie naciśnij klawisze **SHIFT + ENTER** , aby uruchomić.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. Sprawdź, czy dane są przesyłane strumieniowo do programu, `hvactable` uruchamiając następujące zapytanie w SQL Server Management Studio (SSMS). Za każdym razem, gdy uruchamiasz zapytanie, pokazuje liczbę wierszy w tabeli.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Następne kroki

* [Analizowanie danych w Data Lake Storage przy użyciu klastra usługi HDInsight Spark](apache-spark-use-with-data-lake-store.md)
* [Ładowanie danych i uruchamianie zapytań w klastrze Apache Spark w usłudze Azure HDInsight](apache-spark-load-data-run-query.md)
* [Używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
