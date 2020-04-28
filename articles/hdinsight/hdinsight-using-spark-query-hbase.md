---
title: Korzystanie z platformy Spark do odczytywania i zapisywania danych HBase — Azure HDInsight
description: Za pomocą łącznika Spark HBase można odczytywać i zapisywać dane z klastra Spark do klastra HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: e5d9d4f215752d95ee1d676e8a5b126b6d0d3ab2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190626"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Odczytywanie i zapisywanie danych w bazie danych Apache HBase za pomocą platformy Apache Spark

Na platformie Apache HBase są zwykle wykonywane zapytania przy użyciu interfejsu API niskiego poziomu (skanowanie, pobieranie i umieszczanie) lub ze składnią SQL przy użyciu Apache Phoenix. Apache udostępnia również łącznik Apache Spark HBase. Łącznik jest wygodną i wydajną alternatywą dla zapytań i modyfikacji danych przechowywanych przez HBase.

## <a name="prerequisites"></a>Wymagania wstępne

* Dwa oddzielne klastry usługi HDInsight wdrożone w tej samej [sieci wirtualnej](./hdinsight-plan-virtual-network-deployment.md). Jeden HBase i jeden Spark z zainstalowanym co najmniej platformą Spark 2,1 (HDInsight 3,6). Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

* Schemat identyfikatora URI magazynu podstawowego klastrów. Ten schemat byłby wasb://dla Blob Storage platformy Azure `abfs://` dla Azure Data Lake Storage Gen2 lub adl://dla Azure Data Lake Storage Gen1. W przypadku włączenia bezpiecznego transferu dla Blob Storage, identyfikator URI to `wasbs://`.  Zobacz również [bezpieczny transfer](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Proces ogólny

Proces wysokiego poziomu służący do włączania klastra platformy Spark na potrzeby wysyłania zapytań do klastra usługi HDInsight jest następujący:

1. Przygotuj przykładowe dane w HBase.
2. Pobierz plik HBase-site. XML z folderu konfiguracji klastra HBase (/etc/HBase/conf).
3. Umieść kopię pliku HBase-site. XML w folderze konfiguracji Spark 2 (/etc/spark2/conf).
4. Uruchom `spark-shell` odwołujące się do łącznika Spark HBase za pomocą `packages` współrzędnych Maven w opcji.
5. Zdefiniuj katalog, który mapuje schemat z platformy Spark do HBase.
6. Współpracuj z danymi HBase przy użyciu interfejsów API RDD lub Dataframe.

## <a name="prepare-sample-data-in-apache-hbase"></a>Przygotowywanie przykładowych danych w Apache HBase

W tym kroku utworzysz i wypełnimy tabelę w Apache HBase, którą można następnie wysyłać zapytania przy użyciu platformy Spark.

1. Użyj polecenia `ssh` , aby nawiązać połączenie z klastrem HBase. Edytuj poniższe polecenie, zastępując `HBASECLUSTER` je nazwą klastra HBase, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Użyj `hbase shell` polecenia, aby uruchomić powłokę interaktywną HBase. Wprowadź następujące polecenie w połączeniu SSH:

    ```bash
    hbase shell
    ```

3. Użyj polecenia `create` , aby utworzyć tabelę HBase z rodziną dwóch kolumn. Wprowadź następujące polecenie:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Użyj polecenia `put` , aby wstawić wartości z określonej kolumny w określonym wierszu w określonej tabeli. Wprowadź następujące polecenie:

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

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Kopiuj HBase-site. XML do klastra Spark

Skopiuj plik HBase-site. XML z magazynu lokalnego do katalogu głównego magazynu platformy Spark w klastrze domyślnym.  Edytuj poniższe polecenie, aby odzwierciedlić konfigurację.  Następnie z otwartej sesji SSH do klastra HBase wprowadź polecenie:

| Wartość składni | Nowa wartość|
|---|---|
|[Schemat identyfikatora URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Zmodyfikuj, aby odzwierciedlić magazyn.  Poniższa składnia dotyczy usługi BLOB Storage z włączonym bezpiecznym transferem.|
|`SPARK_STORAGE_CONTAINER`|Zastąp wartość domyślną nazwą kontenera magazynu używaną dla klastra Spark.|
|`SPARK_STORAGE_ACCOUNT`|Zamień na domyślną nazwę konta magazynu używanego dla klastra Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Następnie zamknij połączenie SSH z klastrem HBase.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Umieszczanie HBase-site. XML w klastrze Spark

1. Połącz się z węzłem głównym klastra platformy Spark przy użyciu protokołu SSH. Edytuj poniższe polecenie, zastępując `SPARKCLUSTER` je nazwą klastra Spark, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Wprowadź poniższe polecenie, aby skopiować `hbase-site.xml` z domyślnego magazynu klastra Spark do folderu konfiguracji platformy Spark 2 w lokalnym magazynie klastra:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Uruchom powłokę platformy Spark odwołującą się do łącznika Spark HBase

1. Z otwartej sesji SSH do klastra Spark Wprowadź poniższe polecenie, aby uruchomić powłokę Spark:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Pozostaw otwarte wystąpienie powłoki Spark i przejdź do następnego kroku.

## <a name="define-a-catalog-and-query"></a>Definiowanie wykazu i zapytania

W tym kroku zdefiniujesz obiekt katalogu, który mapuje schemat z Apache Spark na Apache HBase.  

1. W otwartej powłoki platformy Spark wprowadź następujące `import` instrukcje:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Wprowadź poniższe polecenie, aby zdefiniować katalog dla tabeli kontaktów utworzonej w programie HBase:

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

     a. Zdefiniuj schemat wykazu dla tabeli HBase o nazwie `Contacts`.  
     b. Zidentyfikuj rowkey jako `key`i zamapuj nazwy kolumn używanych w platformie Spark do rodziny kolumn, nazwy kolumny i typu kolumny jako używane w HBase.  
     c. Rowkey również musi być zdefiniowana w szczegółach jako nazwana kolumna (`rowkey`), która ma określoną rodzinę `cf` kolumn. `rowkey`  

1. Wprowadź poniższe polecenie, aby zdefiniować metodę, która udostępnia ramkę danych wokół `Contacts` tabeli w HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Utwórz wystąpienie elementu Dataframe:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Zbadaj ramkę danych:

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

1. Zarejestruj tabelę tymczasową, aby można było wysyłać zapytania do tabeli HBase przy użyciu platformy Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Wydaj zapytanie SQL względem `contacts` tabeli:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Powinny być widoczne następujące wyniki:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Wstaw nowe dane

1. Aby wstawić nowy rekord kontaktu, zdefiniuj `ContactRecord` klasę:

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

1. Zapisz tablicę nowych danych do HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Przejrzyj wyniki:

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

1. Zamknij powłokę Spark, wprowadzając następujące polecenie:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Następne kroki

* [Apache Spark łącznik HBase](https://github.com/hortonworks-spark/shc)
