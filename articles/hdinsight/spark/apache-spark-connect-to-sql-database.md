---
title: Używanie aplikacji Apache Spark do odczytu i zapisu danych w bazie danych SQL usługi Azure
description: Dowiedz się, jak skonfigurować połączenie między klastrem platformy SPARK usługi HDInsight a bazą danych SQL Azure. Aby odczytywać dane, zapisywać dane i przesyłać strumieniowo dane do bazy danych SQL
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 4e783a233bd35e012c02fbbbdc7f4223552fc734
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686846"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Używanie klastra platformy SPARK usługi HDInsight do odczytywania i zapisywania danych w bazie danych SQL usługi Azure

Dowiedz się, jak połączyć klaster Platformy Spark apache w usłudze Azure HDInsight z bazą danych SQL Azure. Następnie odczytu, zapisu i strumienia danych do bazy danych SQL. Instrukcje w tym artykule używają notesu Jupyter do uruchamiania fragmentów kodu Scala. Można jednak utworzyć autonomiczną aplikację w Scali lub Pythonie i wykonać te same zadania.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster platformy Azure HDInsight Spark.  Postępuj zgodnie z instrukcjami w [utwórz klaster Platformy Spark Apache w pliku HDInsight](apache-spark-jupyter-spark-sql.md).

* usługą Azure SQL Database. Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia bazy danych SQL platformy Azure](../../sql-database/sql-database-get-started-portal.md). Upewnij się, że tworzysz bazę danych z przykładowym schematem **AdventureWorksLT** i danymi. Upewnij się również, że utworzysz regułę zapory na poziomie serwera, aby umożliwić adresowi IP klienta dostęp do bazy danych SQL na serwerze. Instrukcje dotyczące dodawania reguły zapory są dostępne w tym samym artykule. Po utworzeniu bazy danych SQL Azure upewnij się, że masz pod ręką następujące wartości. Są one potrzebne do łączenia się z bazą danych z klastra platformy Spark.

    * Nazwa serwera obsługującego usługę Azure SQL Database.
    * Nazwa bazy danych SQL platformy Azure.
    * Nazwa użytkownika/ hasło administratora bazy danych SQL azure.

* SQL Server Management Studio (SSMS). Postępuj zgodnie z instrukcjami podanymi w [instrukcji SSMS do łączenia się i wykonywania zapytań o dane](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

Zacznij od utworzenia notesu Jupyter skojarzonego z klastrem Platformy Spark. Ten notes służy do uruchamiania fragmentów kodu używanych w tym artykule.

1. Z [witryny Azure portal](https://portal.azure.com/)otwórz klaster.
1. Wybierz **pozycję Notes Jupyter** pod **pulpitami nawigacyjnymi klastra** po prawej stronie.  Jeśli nie widzisz **pulpitów nawigacyjnych klastra,** wybierz **pozycję Przegląd** z lewego menu. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

    ![Notebook Jupyter na Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Notebook Jupyter na Spark")

   > [!NOTE]  
   > Możesz również uzyskać dostęp do notesu Jupyter w klastrze Spark, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. W notesie Jupyter w prawym górnym rogu kliknij pozycję **Nowy**, a następnie kliknij pozycję **Spark,** aby utworzyć notes Scala. Notesy Jupyter w klastrze PLATFORMY SPARK USŁUGI HDInsight Spark zapewniają również jądro **PySpark** dla aplikacji Python2 i jądro **PySpark3** dla aplikacji Python3. W tym artykule tworzymy notes Scala.

    ![Jądra dla notebooka Jupyter na Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Jądra dla notebooka Jupyter na Spark")

    Aby uzyskać informacje na temat tych jąder, zobacz [Use Jupyter notebooks kernels with Apache Spark clusters in HDInsight](apache-spark-jupyter-notebook-kernels.md) (Używanie jąder notesu Jupyter z klastrami Apache Spark w usłudze HDInsight).

   > [!NOTE]  
   > W tym artykule używamy jądra Spark (Scala), ponieważ przesyłanie strumieniowe danych z platformy Spark do bazy danych SQL jest obecnie obsługiwane tylko w scalii i javie. Mimo że czytanie i zapisywanie w języku SQL można wykonać przy użyciu języka Python, dla spójności w tym artykule używamy Scala dla wszystkich trzech operacji.

1. Zostanie otwarty nowy notes o domyślnej nazwie **Bez tytułu**. Kliknij nazwę notesu i wprowadź wybraną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Wprowadzanie nazwy notesu")

Teraz możesz rozpocząć tworzenie aplikacji.

## <a name="read-data-from-azure-sql-database"></a>Odczytywanie danych z bazy danych SQL usługi Azure

W tej sekcji można odczytać dane z tabeli (na przykład **SalesLT.Address**), która istnieje w bazie danych AdventureWorks.

1. W nowym notesie Jupyter w komórce kodu wklej następujący fragment kodu i zastąp wartości zastępcze wartościami bazy danych SQL platformy Azure.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Naciśnij klawisze **SHIFT + ENTER**, aby uruchomić komórkę kodu.  

1. Użyj poniższego fragmentu kodu, aby utworzyć adres URL JDBC, który można przekazać do interfejsów API ram danych platformy Spark. Kod tworzy `Properties` obiekt do przechowywania parametrów. Wklej fragment kodu w komórce kodu i naciśnij **klawisze SHIFT + ENTER,** aby uruchomić.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Użyj fragmentu kodu poniżej, aby utworzyć ramkę danych z danymi z tabeli w bazie danych SQL azure. W tym urywek używamy `SalesLT.Address` tabeli, która jest dostępna jako część **adventureworkslt** bazy danych. Wklej fragment kodu w komórce kodu i naciśnij **klawisze SHIFT + ENTER,** aby uruchomić.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Teraz można wykonywać operacje na ramie danych, takie jak uzyskiwanie schematu danych:

    ```scala
    sqlTableDF.printSchema
    ```

    Zostanie wyświetlenie wyjścia podobnego do następującego obrazu:

    ![dane wyjściowe schematu](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "dane wyjściowe schematu")

1. Można również wykonać operacje, takie jak, pobrać 10 wierszy.

    ```scala
    sqlTableDF.show(10)
    ```

1. Możesz też pobrać określone kolumny z zestawu danych.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Zapisywanie danych w bazie danych SQL usługi Azure

W tej sekcji używamy przykładowego pliku CSV dostępnego w klastrze, aby utworzyć tabelę w usłudze Azure SQL Database i wypełnić ją danymi. Przykładowy plik CSV **(HVAC.csv)** jest dostępny we `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`wszystkich klastrach HDInsight pod adresem .

1. W nowym notesie Jupyter w komórce kodu wklej następujący fragment kodu i zastąp wartości zastępcze wartościami bazy danych SQL platformy Azure.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Naciśnij klawisze **SHIFT + ENTER**, aby uruchomić komórkę kodu.  

1. Poniższy fragment kodu tworzy adres URL JDBC, który można przekazać do interfejsów API ram danych platformy Spark. Kod tworzy `Properties` obiekt do przechowywania parametrów. Wklej fragment kodu w komórce kodu i naciśnij **klawisze SHIFT + ENTER,** aby uruchomić.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Użyj następującego fragmentu kodu, aby wyodrębnić schemat danych w pliku HVAC.csv i użyć schematu do `readDf`załadowania danych z pliku CSV w ramce danych, . Wklej fragment kodu w komórce kodu i naciśnij **klawisze SHIFT + ENTER,** aby uruchomić.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Użyj `readDf` ramki danych, aby `temphvactable`utworzyć tabelę tymczasową , . Następnie użyj tabeli tymczasowej, aby `hvactable_hive`utworzyć tabelę gałęzi, .

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Na koniec użyj tabeli gałęzi, aby utworzyć tabelę w bazie danych SQL azure. Poniższy fragment kodu `hvactable` tworzy w usłudze Azure SQL Database.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Połącz się z usługą Azure SQL Database przy `dbo.hvactable` użyciu usługi SSMS i sprawdź, czy jest widoczna.

    a. Uruchom usługę SSMS i połącz się z usługą Azure SQL Database, udostępniając szczegóły połączenia, jak pokazano na poniższym zrzucie ekranu.

    ![Łączenie się z bazą danych SQL przy użyciu usługi SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Łączenie się z bazą danych SQL przy użyciu usługi SSMS1")

    b. Z **Eksploratora obiektów**rozwiń bazę danych SQL azure i węzeł tabeli, aby wyświetlić utworzony **plik dbo.hvactable.**

    ![Łączenie się z bazą danych SQL przy użyciu usługi SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Łączenie się z bazą danych SQL przy użyciu usługi SSMS2")

1. Uruchom kwerendę w programie SSMS, aby wyświetlić kolumny w tabeli.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Przesyłanie strumieniowe danych do bazy danych SQL usługi Azure

W tej sekcji możemy przesyłać strumieniowo dane do `hvactable` danych, które zostały już utworzone w usłudze Azure SQL Database w poprzedniej sekcji.

1. W pierwszym kroku upewnij się, że `hvactable`w pliku . Korzystając z usługi SSMS, uruchom następującą kwerendę w tabeli.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Utwórz nowy notes Jupyter w klastrze HDInsight Spark. W komórce kodu wklej następujący fragment kodu, a następnie naciśnij **klawisze SHIFT + ENTER**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Przesyłamy strumieniowo dane z pliku `hvactable` **HVAC.csv** do pliku . Plik HVAC.csv jest dostępny `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`w klastrze pod adresem . We wzorzec poniżej najpierw otrzymujemy schemat danych, które mają być przesyłane strumieniowo. Następnie tworzymy ramkę danych przesyłania strumieniowego przy użyciu tego schematu. Wklej fragment kodu w komórce kodu i naciśnij **klawisze SHIFT + ENTER,** aby uruchomić.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. Dane wyjściowe pokazują schemat **pliku HVAC.csv**. Ma `hvactable` ten sam schemat, jak również. Dane wyjściowe zawierają listę kolumn w tabeli.

    !['hdinsight Apache Spark schema table'](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schemat tabeli")

1. Na koniec użyj następującego fragmentu kodu, aby odczytać dane z pliku `hvactable` HVAC.csv i przesłać je strumieniowo do bazy danych SQL w usłudze Azure. Wklej fragment kodu w komórce kodu, zastąp wartości zastępcze wartościami bazy danych SQL Azure, a następnie naciśnij **klawisze SHIFT + ENTER,** aby je uruchomić.

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

1. Sprawdź, czy dane są przesyłane strumieniowo do, `hvactable` uruchamiając następującą kwerendę w programie SQL Server Management Studio (SSMS). Za każdym razem, gdy uruchamiasz kwerendę, pokazuje liczbę wierszy w tabeli zwiększenie.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Następne kroki

* [Używanie klastra hdinsight Spark do analizowania danych w pamięci masowej usługi Data Lake](apache-spark-use-with-data-lake-store.md)
* [Ładowanie danych i uruchamianie zapytań w klastrze Platformy Spark apache w usłudze Azure HDInsight](apache-spark-load-data-run-query.md)
* [Używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
