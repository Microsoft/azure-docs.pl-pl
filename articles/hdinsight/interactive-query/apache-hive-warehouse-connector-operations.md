---
title: Operacje Apache Spark obsługiwane przez łącznik magazynu Hive w usłudze Azure HDInsight
description: Poznaj różne możliwości łącznika magazynu Hive w usłudze Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 1799aff8bff96d404ddcbefbf58a5f5014cdba6a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871592"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Operacje Apache Spark obsługiwane przez łącznik magazynu Hive w usłudze Azure HDInsight

W tym artykule przedstawiono operacje oparte na platformie Spark obsługiwane przez łącznik magazynu Hive (obsługiwane). Wszystkie przykłady przedstawione poniżej zostaną wykonane za pomocą powłoki Apache Spark.

## <a name="prerequisite"></a>Wymaganie wstępne

Ukończ kroki [konfiguracji łącznika magazynu Hive](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) .

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć sesję platformy Spark-Shell, wykonaj następujące czynności:

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) , aby nawiązać połączenie z klastrem Apache Spark. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. W sesji SSH wykonaj następujące polecenie, aby zanotować `hive-warehouse-connector-assembly` wersję:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Edytuj Poniższy kod w `hive-warehouse-connector-assembly` wersji wskazanej powyżej. Następnie wykonaj polecenie, aby uruchomić powłokę Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Po uruchomieniu powłoki Spark można uruchomić wystąpienie łącznika magazynu usługi Hive przy użyciu następujących poleceń:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Tworzenie ramek danych Spark przy użyciu zapytań programu Hive

Wyniki wszystkich zapytań używających biblioteki obsługiwane są zwracane jako ramka danych. W poniższych przykładach pokazano, jak utworzyć podstawową kwerendę programu Hive.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Wyniki zapytania to Spark dataframes, które mogą być używane z bibliotekami Spark, takimi jak MLIB i SparkSQL.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Zapisywanie ramek danych Spark w tabelach Hive

Platforma Spark nie obsługuje natywnego zapisywania w tabelach z zarządzanym KWASem Hive. Jednak za pomocą obsługiwane można napisać dowolna ramka danych do tabeli programu Hive. Tę funkcję można zobaczyć w pracy w następującym przykładzie:

1. Utwórz tabelę o nazwie `sampletable_colorado` i określ jej kolumny przy użyciu następującego polecenia:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Odfiltruj tabelę, `hivesampletable` w której `state` jest równa kolumna `Colorado` . Ta kwerenda Hive zwraca element Spark Dataframe ANS usługi SIS zapisany w tabeli Hive `sampletable_colorado` przy użyciu `write` funkcji.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Wyświetl wyniki przy użyciu następującego polecenia:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png" alt-text="Łącznik magazynu Hive pokazuje tabelę programu Hive" border="true":::


## <a name="structured-streaming-writes"></a>Zapisy przesyłania strumieniowego strukturalnego

Korzystając z łącznika magazynu Hive, można zapisywać dane w tabelach programu Hive przy użyciu funkcji przesyłania strumieniowego platformy Spark.

> [!IMPORTANT]
> Zapisy w ramach przesyłania strumieniowego strukturalnego nie są obsługiwane w klastrach Spark 4,0 w ramach protokołu ESP.

Wykonaj poniższe kroki, aby pozyskać dane ze strumienia Spark na hoście lokalnym 9999 w tabeli programu Hive za pomocą polecenia. Łącznik magazynu Hive.

1. Z otwartej powłoki platformy Spark Rozpocznij Strumień Spark za pomocą następującego polecenia:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Wygeneruj dane dla utworzonego strumienia Spark, wykonując następujące czynności:
    1. Otwórz drugą sesję SSH w tym samym klastrze Spark.
    1. W wierszu polecenia wpisz polecenie `nc -lk 9999` . To polecenie używa narzędzia netcat do wysyłania danych z wiersza polecenia do określonego portu.

1. Wróć do pierwszej sesji SSH i Utwórz nową tabelę programu Hive do przechowywania danych przesyłanych strumieniowo. W powłoce Spark wprowadź następujące polecenie:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Następnie Zapisz dane przesyłane strumieniowo do nowo utworzonej tabeli przy użyciu następującego polecenia:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri`Opcje i `database` należy obecnie ustawić ręcznie z powodu znanego problemu w Apache Spark. Aby uzyskać więcej informacji o tym problemie, zobacz [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Wróć do drugiej sesji SSH i wprowadź następujące wartości:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Wróć do pierwszej sesji SSH i zanotuj krótkie działanie. Użyj następującego polecenia, aby wyświetlić dane:

    ```scala
    hive.table("stream_table").show()
    ```

Użyj **klawiszy CTRL + C** , aby zatrzymać netcat na drugiej sesji SSH. Użyj polecenia `:q` , aby wyjść z powłoki Spark w pierwszej sesji SSH.

## <a name="next-steps"></a>Następne kroki

* [Integracja biblioteki HWC z platformą Apache Spark i programem Apache Hive](./apache-hive-warehouse-connector.md)
* [Używanie zapytań interakcyjnych w usłudze HDInsight](./apache-interactive-query-get-started.md)
* [Integracja biblioteki HWC z rozwiązaniem Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
