---
title: Używanie platformy Spark do odczytywania i zapisywania danych HBase — Azure HDInsight
description: Użyj łącznika Spark HBase, aby odczytywać i zapisywać dane z klastra Platformy Spark w klastrze HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 4f2e8b2a691a6b17b5ed075745d556db4e330535
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682463"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Odczytywanie i zapisywanie danych w bazie danych Apache HBase za pomocą platformy Apache Spark

Apache HBase jest zazwyczaj wyszukiwane za pomocą interfejsu API niskiego poziomu (skanuje, pobiera i stawia) lub ze składnią SQL przy użyciu Apache Phoenix. Apache zapewnia również Apache Spark HBase Connector. Łącznik jest wygodną i wydajną alternatywą dla zapytań i modyfikowania danych przechowywanych przez HBase.

## <a name="prerequisites"></a>Wymagania wstępne

* Dwa oddzielne klastry HDInsight wdrożone w tej samej [sieci wirtualnej](./hdinsight-plan-virtual-network-deployment.md). Jeden HBase i jeden Spark z co najmniej Spark 2.1 (HDInsight 3.6) zainstalowany. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu portalu Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Schemat identyfikatorów URI dla magazynu podstawowego klastrów. Ten schemat zostanie wasb:// dla usługi Azure `abfs://` Blob Storage dla usługi Azure Data Lake Storage Gen2 lub adl:// dla usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla magazynu obiektów `wasbs://`blob, identyfikator URI będzie .  Zobacz też [bezpieczny transfer](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Cały proces

Proces wysokiego poziomu umożliwiający klastrowi platformy Spark wykonywanie zapytań do klastra usługi HDInsight jest następujący:

1. Przygotuj przykładowe dane w bazie danych HBase.
2. Uzyskaj plik hbase-site.xml z folderu konfiguracji klastra HBase (/etc/hbase/conf).
3. Umieść kopię pliku hbase-site.xml w folderze konfiguracyjnym Spark 2 (/etc/spark2/conf).
4. Uruchom `spark-shell` odwołując się do łącznika Spark `packages` HBase przez jego współrzędne Maven w opcji.
5. Zdefiniuj katalog, który mapuje schemat z Platformy Spark do bazy HBase.
6. Interakcja z danymi HBase przy użyciu interfejsów API RDD lub DataFrame.

## <a name="prepare-sample-data-in-apache-hbase"></a>Przygotowywanie przykładowych danych w bazie apache HBase

W tym kroku należy utworzyć i wypełnić tabelę w Apache HBase, które następnie można zbadać przy użyciu platformy Spark.

1. Użyj `ssh` polecenia, aby połączyć się z klastrem HBase. Edytuj poniższe polecenie, zastępując `HBASECLUSTER` nazwą klastra HBase, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Użyj `hbase shell` polecenia, aby uruchomić powłokę interaktywną HBase. Wprowadź następujące polecenie w połączeniu SSH:

    ```bash
    hbase shell
    ```

3. Użyj `create` polecenia, aby utworzyć tabelę HBase z rodzinami dwukolumnowym. Wprowadź następujące polecenie:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Polecenie `put` służy do wstawiania wartości w określonej kolumnie w określonym wierszu w określonej tabeli. Wprowadź następujące polecenie:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Użyj `exit` polecenia, aby zatrzymać powłokę interaktywną HBase. Wprowadź następujące polecenie:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Kopiowanie pliku hbase-site.xml do klastra Spark

Skopiuj plik hbase-site.xml z magazynu lokalnego do katalogu głównego domyślnego magazynu klastra Platformy Spark.  Edytuj poniższe polecenie, aby odzwierciedlić konfigurację.  Następnie, z otwartej sesji SSH do klastra HBase, wprowadź polecenie:

| Wartość składni | Nowa wartość|
|---|---|
|[Schemat identyfikatorów URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modyfikuj, aby odzwierciedlić pamięć masową.  Poniższa składnia dotyczy magazynu obiektów blob z włączonym bezpiecznym transferem.|
|`SPARK_STORAGE_CONTAINER`|Zamień na domyślną nazwę kontenera magazynu używaną dla klastra Spark.|
|`SPARK_STORAGE_ACCOUNT`|Zastąp domyślną nazwą konta magazynu używanego dla klastra Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Następnie zamknij połączenie ssh do klastra HBase.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Umieszczanie pliku hbase-site.xml w klastrze platformy Spark

1. Połącz się z węzłem głównym klastra platformy Spark przy użyciu funkcji SSH. Edytuj poniższe polecenie, zastępując `SPARKCLUSTER` nazwą klastra platformy Spark, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Wprowadź poniższe polecenie, aby skopiować `hbase-site.xml` z domyślnego magazynu klastra Platformy Spark do folderu konfiguracyjnego programu Spark 2 w magazynie lokalnym klastra:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Uruchamianie powłoki iskrowej odwołującej się do łącznika bazy iskierki HBase

1. Od otwartej sesji SSH do klastra Spark wprowadź poniższe polecenie, aby rozpocząć powłokę iskrzącą:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Zachowaj to wystąpienie powłoki iskierki otwarte i przejdź do następnego kroku.

## <a name="define-a-catalog-and-query"></a>Definiowanie katalogu i kwerendy

W tym kroku należy zdefiniować obiekt katalogu, który mapuje schemat z Apache Spark do Apache HBase.  

1. W otwartej powłoki spark `import` wprowadź następujące instrukcje:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Wprowadź poniższe polecenie, aby zdefiniować katalog tabeli Kontakty utworzonej w bazie HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    Kod wykonuje następujące czynności:  

     a. Zdefiniuj schemat katalogu dla `Contacts`tabeli HBase o nazwie .  
     b. Zidentyfikuj klucz wiersza jako `key`, i zamapuj nazwy kolumn używane w spark na rodzinę kolumn, nazwę kolumny i typ kolumny, zgodnie z użyciem w bazie danych HBase.  
     d. Klucz wiersza musi być również zdefiniowany szczegółowo`rowkey`jako nazwana kolumna ( ), która ma określoną rodzinę `cf` kolumn `rowkey`.  

1. Wprowadź poniższe polecenie, aby zdefiniować metodę, `Contacts` która udostępnia ramkę data wokół tabeli w bazie HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Utwórz wystąpienie elementu DataFrame:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Zapytanie o ramę dataframe:

    ```scala
    df.show()
    ```

    Powinny być widoczne dwa wiersze danych:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Zarejestruj tabelę tymczasową, aby można było wysyłać kwerendy do tabeli HBase przy użyciu programu Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Wystawianie kwerendy `contacts` SQL względem tabeli:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Powinny być widoczne wyniki takie jak te:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Wstawianie nowych danych

1. Aby wstawić nowy rekord `ContactRecord` kontaktu, zdefiniuj klasę:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Utwórz wystąpienie `ContactRecord` i umieść je w tablicy:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Zapisz tablicę nowych danych w bazie danych HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Sprawdź wyniki:

    ```scala  
    df.show()
    ```

    Powinny pojawić się następujące dane wyjściowe:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Zamknij powłokę iskrową, wprowadzając następujące polecenie:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Następne kroki

* [Złącze Bazy danych Apache Spark HBase](https://github.com/hortonworks-spark/shc)
