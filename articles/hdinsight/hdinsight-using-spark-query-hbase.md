---
title: Korzystanie z platformy Spark do odczytywania i zapisywania danych HBase — Azure HDInsight
description: Za pomocą łącznika Spark HBase można odczytywać i zapisywać dane z klastra Spark do klastra HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 3ddb8734a3d15a6cd5f4a43ee069d6364f7523ed
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087490"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Odczytywanie i zapisywanie danych w bazie danych Apache HBase za pomocą platformy Apache Spark

Na platformie Apache HBase są zwykle wykonywane zapytania przy użyciu interfejsu API niskiego poziomu (skanowanie, pobieranie i umieszczanie) lub ze składnią SQL przy użyciu Apache Phoenix. Apache udostępnia również łącznik Apache Spark HBase. Łącznik jest wygodną i wydajną alternatywą dla zapytań i modyfikacji danych przechowywanych przez HBase.

## <a name="prerequisites"></a>Wymagania wstępne

* Dwa oddzielne klastry usługi HDInsight wdrożone w tej samej [sieci wirtualnej](./hdinsight-plan-virtual-network-deployment.md). Jeden HBase i jeden Spark z zainstalowanym co najmniej platformą Spark 2,1 (HDInsight 3,6). Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

* Schemat identyfikatora URI magazynu podstawowego klastrów. Ten schemat byłby wasb://dla Blob Storage platformy Azure `abfs://` dla Azure Data Lake Storage Gen2 lub ADL://dla Azure Data Lake Storage Gen1. W przypadku włączenia bezpiecznego transferu dla Blob Storage, identyfikator URI to `wasbs://` .  Zobacz również [bezpieczny transfer](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Proces ogólny

Proces wysokiego poziomu służący do włączania klastra platformy Spark na potrzeby wysyłania zapytań do klastra usługi HDInsight jest następujący:

1. Przygotuj przykładowe dane w HBase.
2. Pobierz plik hbase-site.xml z folderu konfiguracji klastra HBase (/etc/HBase/conf).
3. Umieść kopię hbase-site.xml w folderze konfiguracji Spark 2 (/etc/spark2/conf).
4. Uruchom `spark-shell` odwołujące się do łącznika Spark HBase za pomocą współrzędnych Maven w `packages` opcji.
5. Zdefiniuj katalog, który mapuje schemat z platformy Spark do HBase.
6. Współpracuj z danymi HBase przy użyciu interfejsów API RDD lub Dataframe.

## <a name="prepare-sample-data-in-apache-hbase"></a>Przygotowywanie przykładowych danych w Apache HBase

W tym kroku utworzysz i wypełnimy tabelę w Apache HBase, którą można następnie wysyłać zapytania przy użyciu platformy Spark.

1. Użyj `ssh` polecenia, aby nawiązać połączenie z klastrem HBase. Edytuj poniższe polecenie, zastępując je `HBASECLUSTER` nazwą klastra HBase, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Użyj `hbase shell` polecenia, aby uruchomić powłokę interaktywną HBase. Wprowadź następujące polecenie w połączeniu SSH:

    ```bash
    hbase shell
    ```

3. Użyj `create` polecenia, aby utworzyć tabelę HBase z rodziną dwóch kolumn. Wprowadź następujące polecenie:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Użyj `put` polecenia, aby wstawić wartości z określonej kolumny w określonym wierszu w określonej tabeli. Wprowadź następujące polecenie:

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

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Kopiuj hbase-site.xml do klastra Spark

Skopiuj hbase-site.xml z magazynu lokalnego do katalogu głównego magazynu platformy Spark w klastrze domyślnym.  Edytuj poniższe polecenie, aby odzwierciedlić konfigurację.  Następnie z otwartej sesji SSH do klastra HBase wprowadź polecenie:

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

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Umieszczanie hbase-site.xml w klastrze Spark

1. Połącz się z węzłem głównym klastra platformy Spark przy użyciu protokołu SSH. Edytuj poniższe polecenie, zastępując je `SPARKCLUSTER` nazwą klastra Spark, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Wprowadź poniższe polecenie, aby skopiować `hbase-site.xml` z domyślnego magazynu klastra Spark do folderu konfiguracji platformy Spark 2 w lokalnym magazynie klastra:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Uruchom powłokę platformy Spark odwołującą się do łącznika Spark HBase

Po ukończeniu poprzedniego kroku powinno być możliwe uruchomienie powłoki Spark z odwołującą się do odpowiedniej wersji łącznika Spark HBase. Aby znaleźć najnowszą odpowiednią wersję rdzenia łącznika Spark HBase dla scenariusza klastra, zobacz [SHC podstawowe repozytorium](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/).

Na przykład poniższa tabela zawiera listę dwóch wersji i odpowiednich poleceń aktualnie używanych przez zespół usługi HDInsight. Możesz użyć tych samych wersji dla klastrów, jeśli wersje HBase i Spark są takie same, jak wskazano w tabeli. 


1. W otwartej sesji SSH z klastrem Spark wprowadź następujące polecenie, aby uruchomić powłokę Spark:

    |Wersja platformy Spark| Wersja HDI HBase  | Wersja SHC    |  Polecenie  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3,6 (HBase 1,1) | 1.1.0.3.1.2.2-1    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2,4    | HDI 4,0 (HBase 2,0) | 1.1.1-2.1-s_2.11  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. Pozostaw otwarte wystąpienie powłoki Spark i Kontynuuj [Definiowanie wykazu i zapytania](#define-a-catalog-and-query). Jeśli nie znajdziesz Jars odpowiadającego Twoim wersjom w SHC Core repozytorium, Kontynuuj odczytywanie. 

Jars można skompilować bezpośrednio z gałęzi usługi [Spark-HBase-Connector](https://github.com/hortonworks-spark/shc) usługi GitHub. Na przykład jeśli korzystasz z platformy Spark 2,3 i HBase 1,1, wykonaj następujące kroki:

1. Sklonuj repozytorium:

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. Przejdź do gałęzi-2,3:

    ```bash
    git checkout branch-2.3
    ```

3. Kompiluj z gałęzi (tworzy plik JAR):

    ```bash
    mvn clean package -DskipTests
    ```
    
3. Uruchom następujące polecenie (Pamiętaj o zmianie nazwy pliku JAR odpowiadającego skompilowanemu plikowi jar):

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. Pozostaw otwarte wystąpienie powłoki Spark i przejdź do następnej sekcji. 



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

    Kod:  

    1. Definiuje schemat wykazu dla tabeli HBase o nazwie `Contacts` .  
    1. Identyfikuje rowkey jako `key` i mapuje nazwy kolumn używane w Spark do rodziny kolumn, nazwy kolumny i typu kolumny, jak używane w HBase.  
    1. Definiuje rowkey szczegółowo jako nazwę kolumny ( `rowkey` ), która ma określoną rodzinę kolumn `cf` `rowkey` .  

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
