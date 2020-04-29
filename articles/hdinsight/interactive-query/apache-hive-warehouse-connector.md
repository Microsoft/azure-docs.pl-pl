---
title: Apache Spark & Hive — łącznik magazynu Hive — Azure HDInsight
description: Dowiedz się, jak zintegrować Apache Spark i Apache Hive z łącznikiem magazynu Hive w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78252407"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrowanie Apache Spark i Apache Hive z łącznikiem magazynu Hive

Łącznik magazynu Apache Hive (obsługiwane) to biblioteka, która umożliwia łatwiejsze działanie z Apache Spark i Apache Hive przez obsługę zadań, takich jak przeniesienie danych między ramkami dataframes i tabela Hive, a także kierowanie danych strumienia Spark do tabel programu Hive. Łącznik magazynu Hive działa jak Most między platformami Spark i Hive. Obsługuje ona programowanie Scala, Java i Python.

Łącznik magazynu Hive umożliwia korzystanie z unikatowych funkcji programu Hive i platformy Spark w celu tworzenia zaawansowanych aplikacji do obsługi dużych ilości danych. Apache Hive oferuje obsługę transakcji bazy danych, które są niepodzielne, spójne, izolowane i trwałe (kwas). Aby uzyskać więcej informacji o KWASie i transakcjach w programie Hive, zobacz [transakcje Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive oferuje także szczegółowe mechanizmy kontroli zabezpieczeń za poorednictwem oprogramowania Apache Ranger i przetwarzania analitycznego o małym opóźnieniu, które nie jest dostępne w Apache Spark.

Apache Spark ma interfejs API przesyłania strumieniowego ze strukturą, który zapewnia funkcje przesyłania strumieniowego, które nie są dostępne w Apache Hive. Począwszy od usługi HDInsight 4,0, Apache Spark 2.3.1 i Apache Hive 3.1.0 mają oddzielne magazyny metadanych, co może utrudnić współdziałanie. Łącznik magazynu Hive ułatwia korzystanie z platformy Spark i Hive. Biblioteka obsługiwane ładuje dane z demonów LLAP do równoległych modułów wykonujących testy, co zwiększa efektywność i skalowalność niż przy użyciu standardowego połączenia JDBC z platformy Spark do Hive.

![Architektura łącznika magazynu Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Niektóre operacje obsługiwane przez łącznik magazynu Hive są następujące:

* Opisywanie tabeli
* Tworzenie tabeli dla danych w formacie ORC
* Wybieranie danych programu Hive i pobieranie ramki Dataframe
* Zapisywanie ramki danych w usłudze Hive w partii
* Wykonywanie instrukcji UPDATE programu Hive
* Odczytywanie danych tabeli z gałęzi Hive, przekształcanie ich w platformę Spark i zapisywanie ich w nowej tabeli programu Hive
* Zapisywanie ramki Dataframe lub strumienia Spark w usłudze Hive przy użyciu HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Konfiguracja łącznika magazynu Hive

Wykonaj następujące kroki, aby skonfigurować łącznik magazynu Hive między klastrem zapytań Spark i Interactive w usłudze Azure HDInsight:

### <a name="create-clusters"></a>Tworzenie klastrów

1. Utwórz klaster HDInsight Spark **4,0** przy użyciu konta magazynu i niestandardowej sieci wirtualnej platformy Azure. Aby uzyskać informacje na temat tworzenia klastra w sieci wirtualnej platformy Azure, zobacz [Dodawanie usługi HDInsight do istniejącej sieci wirtualnej](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Utwórz klaster programu HDInsight Interactive Query (LLAP) **4,0** przy użyciu tego samego konta magazynu i usługi Azure Virtual Network co klaster Spark.

### <a name="modify-hosts-file"></a>Modyfikuj plik hosts

Skopiuj informacje o węźle z `/etc/hosts` pliku na Headnode0 klastra interakcyjnych zapytań i Połącz informacje z `/etc/hosts` plikiem w headnode0 klastra Spark. Ten krok umożliwi klastrowi Spark rozpoznawanie adresów IP węzłów w klastrze zapytań interaktywnych. Wyświetl zawartość zaktualizowanego pliku w programie `cat /etc/hosts`. Końcowe dane wyjściowe powinny wyglądać podobnie do przedstawionego na poniższym zrzucie ekranu.

![plik hosts łącznika magazynu Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Zbierz informacje wstępne

#### <a name="from-your-interactive-query-cluster"></a>Z interaktywnego klastra zapytań

1. Przejdź do strony klastra Apache Ambari Hive, przy użyciu `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` której `LLAPCLUSTERNAME` jest nazwą klastra zapytań interaktywnych.

1. Przejdź do **zaawansowanych** > **ogólnych** > **bloków Hive. metadanych** i zanotuj wartość. Wartość może być podobna do: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

1. Przejdź do **zaawansowanej** > **zaawansowanej gałęzi Hive-site** > **Hive. dozorcy. kworum** i zanotuj wartość. Wartość może być podobna do: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

#### <a name="from-your-apache-spark-cluster"></a>Z klastra Apache Spark

1. Przejdź do strony klastra Apache Ambari Hive, przy użyciu `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` której `SPARKCLUSTERNAME` jest nazwą klastra Apache Spark.

1. Przejdź do **zaawansowanej** > **zaawansowanej usługi Hive-Interactive-site** > **Hive. llap. Demon. Service. hosts** i zanotuj wartość. Wartość może być podobna do: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Skonfiguruj ustawienia klastra Spark

W interfejsie użytkownika sieci Web platformy Spark Ambari przejdź do opcji **Spark2** > **configs** > **Custom Spark2-Defaults (ustawienia domyślne**).

![Konfiguracja oprogramowania Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Wybierz pozycję **Dodaj właściwość...** w razie potrzeby Dodaj/zaktualizuj następujące elementy:

| Key | Wartość |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Wartość uzyskana wcześniej od programu **Hive. llap. Demon. Service. hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Ustaw wartość parametrów połączenia JDBC, która łączy się z serwera hiveserver2 w klastrze zapytań interaktywnych. Zamień `LLAPCLUSTERNAME` na nazwę klastra zapytań interaktywnych. Zamień `PWD` na rzeczywiste hasło.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Ustaw odpowiedni katalog przemieszczania zgodny z systemem plików HDFS. Jeśli istnieją dwa różne klastry, katalog przemieszczania powinien być folderem w katalogu przemieszczania konta magazynu klastra LLAP, dzięki czemu serwera hiveserver2 ma do niego dostęp.  Zastąp `STORAGE_ACCOUNT_NAME` ciąg nazwą konta magazynu używanego przez klaster i `STORAGE_CONTAINER_NAME` nazwą kontenera magazynu.|
|`spark.datasource.hive.warehouse.metastoreUri`|Wartość uzyskana wcześniej od identyfikatora **URI Hive. metadanych**.|
|`spark.security.credentials.hiveserver2.enabled`|`false`w przypadku trybu wdrażania klienta PRZĘDZenia.|
|`spark.hadoop.hive.zookeeper.quorum`|Wartość uzyskana wcześniej od programu **Hive. dozorcy. kworum**.|

Zapisz zmiany i ponownie uruchom składniki zgodnie z wymaganiami.

## <a name="using-the-hive-warehouse-connector"></a>Korzystanie z łącznika magazynu Hive

### <a name="connecting-and-running-queries"></a>Łączenie i uruchamianie zapytań

Można wybrać kilka różnych metod nawiązywania połączenia z klastrem interakcyjnych zapytań i wykonywać zapytania przy użyciu łącznika magazynu Hive. Obsługiwane metody obejmują następujące narzędzia:

* [Spark — Shell](../spark/apache-spark-shell.md)
* PySpark
* Platforma Spark — przesyłanie
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Wszystkie przykłady podane w tym artykule będą wykonywane za poorednictwem powłoki Spark.

Aby rozpocząć sesję platformy Spark-Shell, wykonaj następujące czynności:

1. Użyj protokołu SSH w węzła głównego dla klastra Apache Spark. Aby uzyskać więcej informacji na temat nawiązywania połączenia z klastrem przy użyciu protokołu SSH, zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Wprowadź następujące polecenie, aby uruchomić powłokę Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Zobaczysz komunikat powitalny i `scala>` monit, gdzie można wprowadzać polecenia.

1. Po uruchomieniu powłoki Spark można uruchomić wystąpienie łącznika magazynu usługi Hive przy użyciu następujących poleceń:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Łączenie i uruchamianie zapytań w przypadku klastrów pakiet Enterprise Security (ESP)

Pakiet Enterprise Security (ESP) oferuje funkcje klasy korporacyjnej, takie jak uwierzytelnianie oparte na Active Directoryach, obsługa przez wiele użytkowników i kontrola dostępu oparta na rolach dla klastrów Apache Hadoop w usłudze Azure HDInsight. Aby uzyskać więcej informacji na temat ESP, zobacz [używanie pakiet Enterprise Security w usłudze HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. Użyj protokołu SSH w węzła głównego dla klastra Apache Spark. Aby uzyskać więcej informacji na temat nawiązywania połączenia z klastrem przy użyciu protokołu SSH, zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Wpisz `kinit` i zaloguj się przy użyciu użytkownika domeny.

1. Uruchom program Spark-Shell z pełną listą parametrów konfiguracji, jak pokazano poniżej. Wszystkie wartości we wszystkich wielkich literach między nawiasami ostrymi należy określić na podstawie klastra. Aby dowiedzieć się więcej na temat wartości wejściowych dla dowolnego z poniższych parametrów, zobacz sekcję dotyczącą [konfiguracji łącznika magazynu usługi Hive](#hive-warehouse-connector-setup).

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Tworzenie ramek dataframes platformy Spark z zapytań programu Hive

Wyniki wszystkich zapytań używających biblioteki obsługiwane są zwracane jako ramka danych. W poniższych przykładach pokazano, jak utworzyć zapytanie podstawowe.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Wyniki zapytania to Spark dataframes, które mogą być używane z bibliotekami Spark, takimi jak MLIB i SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Zapisywanie ramek danych Spark w tabelach Hive

Platforma Spark nie obsługuje natywnego zapisywania w tabelach z zarządzanym KWASem Hive. Za pomocą obsługiwane, można jednak napisać dowolną ramkę danych do tabeli programu Hive. Tę funkcję można zobaczyć w pracy w następującym przykładzie:

1. Utwórz tabelę o nazwie `sampletable_colorado` i określ jej kolumny przy użyciu następującego polecenia:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Odfiltruj `hivesampletable` tabelę, w `state` której `Colorado`jest równa kolumna. To zapytanie tabeli Hive jest zwracane jako ramka Dataframe. Następnie ramka danych jest zapisywana w tabeli `sampletable_colorado` programu Hive przy użyciu `write` funkcji.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Wyświetl wyniki przy użyciu następującego polecenia:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![Łącznik magazynu Hive pokazuje tabelę programu Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Zapisy przesyłania strumieniowego strukturalnego

Korzystając z łącznika magazynu Hive, można zapisywać dane w tabelach programu Hive przy użyciu funkcji przesyłania strumieniowego platformy Spark.

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć przykład łącznika magazynu Hive, który pozyskuje dane ze strumienia Spark w porcie localhost 9999 w tabeli programu Hive.

1. Wykonaj kroki opisane w sekcji [łączenie i uruchamianie zapytań](#connecting-and-running-queries).

1. Rozpocznij Strumień Spark za pomocą następującego polecenia:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Wygeneruj dane dla utworzonego strumienia Spark, wykonując następujące czynności:
    1. Otwórz drugą sesję SSH w tym samym klastrze Spark.
    1. W wierszu polecenia wpisz `nc -lk 9999`polecenie. To polecenie używa narzędzia netcat do wysyłania danych z wiersza polecenia do określonego portu.

1. Wróć do pierwszej sesji SSH i Utwórz nową tabelę programu Hive do przechowywania danych przesyłanych strumieniowo. W powłoce Spark wprowadź następujące polecenie:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Następnie Zapisz dane przesyłane strumieniowo do nowo utworzonej tabeli przy użyciu następującego polecenia:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Opcje `metastoreUri` i `database` należy obecnie ustawić ręcznie z powodu znanego problemu w Apache Spark. Aby uzyskać więcej informacji o tym problemie, zobacz [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

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

Użyj **klawiszy CTRL + C** , aby zatrzymać netcat na drugiej sesji SSH. Użyj `:q` polecenia, aby wyjść z powłoki Spark w pierwszej sesji SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Zabezpieczanie danych w klastrach Spark ESP

1. Utwórz tabelę `demo` z niektórymi przykładowymi danymi, wprowadzając następujące polecenia:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Wyświetl zawartość tabeli przy użyciu następującego polecenia. Przed zastosowaniem zasad `demo` tabela wyświetla pełną kolumnę.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabela demonstracyjna przed zastosowaniem zasad Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Zastosuj zasady maskowania kolumn, które wyświetlają tylko ostatnie cztery znaki kolumny.  
    1. Przejdź do interfejsu użytkownika administratora Ranger pod `https://CLUSTERNAME.azurehdinsight.net/ranger/`adresem.
    1. Kliknij usługę Hive dla klastra w obszarze **Hive**.
        ![Ranger Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Kliknij kartę **maskowanie** , a następnie **Dodaj nowe zasady**

        ![Lista zasad Hive Ranger łącznika magazynu Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Podaj żądaną nazwę zasad. Wybierz bazę danych **: default**, tabela programu Hive: **Demonstracja**, kolumna Hive: **name**, User: **rsadmin2**, typy dostępu: **SELECT**i **maska częściowa: Pokaż ostatnie 4** z menu **opcji wybierz maskowanie** . Kliknij pozycję **Dodaj**.
                ![Utwórz zasady](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Ponownie Wyświetl zawartość tabeli. Po zastosowaniu zasad Ranger można zobaczyć tylko cztery ostatnie znaki w kolumnie.

    ![tabela demonstracyjna po zastosowaniu zasad Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Następne kroki

* [Używanie zapytań interakcyjnych w usłudze HDInsight](./apache-interactive-query-get-started.md)
* [Przykłady współpracy z łącznikiem magazynu Hive przy użyciu Zeppelin, usługi Livy, Spark-Submit i pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
