---
title: Apache Spark & Hive — łącznik magazynu Hive — Azure HDInsight
description: Dowiedz się, jak zintegrować Apache Spark i Apache Hive z łącznikiem magazynu Hive w usłudze Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 57a3d76f24c33984a883e926a8d4c68736e9f121
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869892"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrowanie Apache Spark i Apache Hive z łącznikiem magazynu Hive w usłudze Azure HDInsight

Łącznik magazynu Apache Hive (obsługiwane) to biblioteka, która umożliwia łatwiejsze działanie dzięki Apache Spark i Apache Hive. Obsługuje ona zadania, takie jak przeniesienie danych między ramkami dataframes i Hive. Ponadto przez kierowanie danych strumienia Spark do tabel programu Hive. Łącznik magazynu Hive działa jak Most między platformami Spark i Hive. Obsługuje ona również Scala, Java i Python jako języki programowania do programowania.

Łącznik magazynu Hive umożliwia korzystanie z unikatowych funkcji programu Hive i platformy Spark w celu tworzenia zaawansowanych aplikacji do obsługi dużych ilości danych.

Apache Hive oferuje obsługę transakcji bazy danych, które są niepodzielne, spójne, izolowane i trwałe (kwas). Aby uzyskać więcej informacji o KWASie i transakcjach w programie Hive, zobacz [transakcje Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive oferuje także szczegółowe mechanizmy kontroli zabezpieczeń za poorednictwem oprogramowania Apache Ranger i przetwarzania analitycznego o małym opóźnieniu (LLAP), które nie są dostępne w Apache Spark.

Apache Spark ma interfejs API przesyłania strumieniowego ze strukturą, który zapewnia funkcje przesyłania strumieniowego, które nie są dostępne w Apache Hive. Począwszy od usługi HDInsight 4,0, Apache Spark 2.3.1 i Apache Hive 3.1.0 mają oddzielne magazyny. Osobne magazyny metadanych mogą utrudniać współdziałanie. Łącznik magazynu Hive ułatwia korzystanie z platformy Spark i Hive. Biblioteka obsługiwane ładuje dane z demonów LLAP do równoległych modułów wykonujących testy. Ten proces sprawia, że jest bardziej wydajny i dostosowywalny niż standardowe połączenie JDBC z platformy Spark do Hive.

:::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png" alt-text="Architektura łącznika magazynu Hive" border="true":::

Niektóre operacje obsługiwane przez łącznik magazynu Hive są następujące:

* Opisywanie tabeli
* Tworzenie tabeli dla danych w formacie ORC
* Wybieranie danych programu Hive i pobieranie ramki Dataframe
* Zapisywanie ramki danych w usłudze Hive w partii
* Wykonywanie instrukcji UPDATE programu Hive
* Odczytywanie danych tabeli z gałęzi Hive, przekształcanie ich w platformę Spark i zapisywanie ich w nowej tabeli programu Hive
* Zapisywanie ramki Dataframe lub strumienia Spark w usłudze Hive przy użyciu HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Konfiguracja łącznika magazynu Hive

> [!IMPORTANT]
> - Wystąpienie interaktywne serwera hiveserver2 zainstalowane w klastrach pakiet Enterprise Security platformy Spark 2,4 nie jest obsługiwane do użycia z łącznikiem magazynu Hive. Zamiast tego należy skonfigurować oddzielny klaster serwera hiveserver2 Interactive do hostowania interaktywnych obciążeń serwera hiveserver2. Konfiguracja łącznika magazynu programu Hive wykorzystująca pojedynczy klaster Spark 2,4 nie jest obsługiwana.
> - Biblioteka łącznika magazynu Hive (obsługiwane) nie jest obsługiwana w przypadku klastrów zapytań interaktywnych, w których włączona jest funkcja zarządzania obciążeniami (WLM). <br>
W scenariuszu, w którym masz tylko obciążenia platformy Spark i chcesz korzystać z biblioteki obsługiwane, upewnij się, że w klastrze zapytań interaktywnych nie jest włączona funkcja zarządzania obciążeniami ( `hive.server2.tez.interactive.queue` Konfiguracja nie jest ustawiona w konfiguracjach Hive). <br>
W przypadku scenariusza, w którym istnieją zarówno obciążenia Spark (obsługiwane), jak i LLAP natywne, należy utworzyć dwa oddzielne klastry zapytań interaktywnych z udostępnioną bazą danych magazynu metadanych. Jeden klaster dla natywnych obciążeń LLAP, w których funkcja WLM może być włączona na potrzeby i innych klastrach w celu obsługiwane obciążenia, w przypadku których nie należy konfigurować funkcji WLM.
Należy pamiętać, że można wyświetlić plany zasobów WLM z obu klastrów, nawet jeśli są one włączone tylko w jednym klastrze. Nie wprowadzaj żadnych zmian w planach zasobów w klastrze, w którym funkcja WLM jest wyłączona, ponieważ może to mieć wpływ na funkcjonalność WLM w innym klastrze.

Łącznik magazynu Hive wymaga oddzielnych klastrów dla obciążeń Spark i Interactive zapytań. Wykonaj następujące kroki, aby skonfigurować te klastry w usłudze Azure HDInsight.

### <a name="create-clusters"></a>Tworzenie klastrów

1. Utwórz klaster HDInsight Spark **4,0** przy użyciu konta magazynu i niestandardowej sieci wirtualnej platformy Azure. Aby uzyskać informacje na temat tworzenia klastra w sieci wirtualnej platformy Azure, zobacz [Dodawanie usługi HDInsight do istniejącej sieci wirtualnej](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Utwórz klaster programu HDInsight Interactive Query (LLAP) **4,0** przy użyciu tego samego konta magazynu i usługi Azure Virtual Network co klaster Spark.

### <a name="configure-hwc-settings"></a>Konfigurowanie ustawień obsługiwane

#### <a name="gather-preliminary-information"></a>Zbierz informacje wstępne

1. W przeglądarce sieci Web przejdź do `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE` lokalizacji, gdzie LLAPCLUSTERNAME jest nazwą klastra zapytań interaktywnych.

1. Przejdź do **podsumowania**  >  **serwera hiveserver2 Interactive JDBC URL** i zanotuj wartość. Wartość może być podobna do: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive` .

1. Przejdź do strony **configs**  >  **Advanced**  >  **Advanced Hive-site**  >  **Hive. dozorcy. kworum** i zanotuj wartość. Wartość może być podobna do: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181` .

1. Przejdź do **konfiguracji**  >  **Zaawansowane**  >  **Ogólne**  >  **Hive. metadanych magazynu. URI** i zanotuj wartość. Wartość może być podobna do: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083` .

1. Przejdź do strony **configs**  >  **Advanced**  >  **Advanced Hive-Interactive-site**  >  **Hive. llap. Demon. Service. hosts** i zanotuj wartość. Wartość może być podobna do: `@llap0` .

#### <a name="configure-spark-cluster-settings"></a>Skonfiguruj ustawienia klastra Spark

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` lokalizacji, gdzie ClusterName jest nazwą klastra Apache Spark.

1. Rozwiń węzeł **niestandardowe spark2 — ustawienia domyślne**.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png" alt-text="Konfiguracja oprogramowania Apache Ambari Spark2" border="true":::

1. Wybierz pozycję **Dodaj właściwość...** , aby dodać następujące konfiguracje:

    | Konfigurowanie | Wartość |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> Ustaw odpowiedni katalog przemieszczania zgodny z systemem plików HDFS. Jeśli istnieją dwa różne klastry, katalog przemieszczania powinien być folderem w katalogu przemieszczania konta magazynu klastra LLAP, dzięki czemu serwera hiveserver2 ma do niego dostęp.  Zastąp ciąg `STORAGE_ACCOUNT_NAME` nazwą konta magazynu używanego przez klaster i `STORAGE_CONTAINER_NAME` nazwą kontenera magazynu. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| Wartość uzyskana wcześniej od **serwera hiveserver2 Interactive JDBC URL** |
    |`spark.datasource.hive.warehouse.metastoreUri`| Wartość uzyskana wcześniej od identyfikatora **URI Hive. metadanych**. |
    |`spark.security.credentials.hiveserver2.enabled`|`true` w przypadku trybu klastra PRZĘDZy i `false` trybu klienta przędzy. |
    |`spark.hadoop.hive.zookeeper.quorum`| Wartość uzyskana wcześniej od programu **Hive. dozorcy. kworum**. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| Wartość uzyskana wcześniej od programu **Hive. llap. Demon. Service. hosts**. |

1. Zapisz zmiany i ponownie uruchom wszystkie składniki, których to dotyczy.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>Konfigurowanie klastrów obsługiwane for pakiet Enterprise Security (ESP)

Pakiet Enterprise Security (ESP) oferuje funkcje klasy korporacyjnej, takie jak uwierzytelnianie oparte na Active Directoryach, obsługa przez wiele użytkowników i kontrola dostępu oparta na rolach dla klastrów Apache Hadoop w usłudze Azure HDInsight. Aby uzyskać więcej informacji na temat ESP, zobacz [używanie pakiet Enterprise Security w usłudze HDInsight](../domain-joined/apache-domain-joined-architecture.md).

Poza konfiguracjami wymienionymi w poprzedniej sekcji Dodaj następującą konfigurację, aby użyć obsługiwane w klastrach ESP.

1. Z Ambari internetowego interfejsu użytkownika klastra Spark przejdź do opcji **Spark2**  >  **configs**  >  **Custom Spark2-Defaults (ustawienia domyślne**).

1. Zaktualizuj poniższą właściwość.

    | Konfigurowanie | Wartość |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` lokalizacji, gdzie ClusterName jest nazwą klastra interakcyjnych zapytań. Kliknij pozycję **serwera hiveserver2 Interactive**. Zostanie wyświetlona w pełni kwalifikowana nazwa domeny (FQDN) węzła głównego, na którym działa LLAP, jak pokazano na zrzucie ekranu. Zamień `<llap-headnode>` na tę wartość.

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="Węzeł główny łącznika magazynu Hive" border="true":::

    * Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem interakcyjnych zapytań. Wyszukaj `default_realm` parametr w `/etc/krb5.conf` pliku. Zamień `<AAD-DOMAIN>` na tę wartość jako ciąg pisany wielkimi literami, w przeciwnym razie nie znaleziono poświadczeń.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="Domena usługi AAD magazynu usługi Hive" border="true":::

    * Na przykład `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .
    
1. Zapisz zmiany i ponownie uruchom składniki zgodnie z wymaganiami.

## <a name="hive-warehouse-connector-usage"></a>Użycie łącznika magazynu Hive

Można wybrać kilka różnych metod nawiązywania połączenia z klastrem interakcyjnych zapytań i wykonywać zapytania przy użyciu łącznika magazynu Hive. Obsługiwane metody obejmują następujące narzędzia:

* [Spark-Shell/PySpark](../spark/apache-spark-shell.md)
* [Platforma Spark — przesyłanie](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Poniżej przedstawiono kilka przykładów łączenia się z usługą obsługiwane z platformy Spark.

### <a name="spark-shell"></a>Spark — Shell

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
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Po uruchomieniu powłoki Spark wystąpienie łącznika magazynu usługi Hive można uruchomić przy użyciu następujących poleceń:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Platforma Spark — przesyłanie

Po skompilowaniu kodu Scala/Java wraz z zależnościami na słoik zestawu, użyj poniższego polecenia, aby uruchomić aplikację Spark. Zastąp `<VERSION>` wartości i wartościami `<APP_JAR_PATH>` rzeczywistymi.

* Tryb klienta PRZĘDZy
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* Tryb klastra PRZĘDZy
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

W przypadku języka Python należy również dodać następującą konfigurację. 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Uruchamianie zapytań dotyczących klastrów pakiet Enterprise Security (ESP)

Użyj `kinit` przed rozpoczęciem uruchamiania powłoki Spark lub Spark. Zastąp nazwę USERNAME nazwą konta domeny z uprawnieniami dostępu do klastra, a następnie wykonaj następujące polecenie:

```bash
kinit USERNAME
```

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

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png" alt-text="tabela demonstracyjna przed zastosowaniem zasad Ranger" border="true":::

1. Zastosuj zasady maskowania kolumn, które wyświetlają tylko ostatnie cztery znaki kolumny.  
    1. Przejdź do interfejsu użytkownika administratora Ranger pod adresem `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/` .
    1. Kliknij usługę Hive dla klastra w obszarze **Hive**.
        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png" alt-text="Ranger Service Manager" border="true":::
    1. Kliknij kartę **maskowanie** , a następnie **Dodaj nowe zasady**

        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png" alt-text="Lista zasad Hive Ranger łącznika magazynu Hive" border="true":::

    1. Podaj żądaną nazwę zasad. Wybierz bazę danych **: default**, tabela programu Hive: **Demonstracja**, kolumna Hive: **name**, User: **rsadmin2**, typy dostępu: **SELECT** i **maska częściowa: Pokaż ostatnie 4** z menu **opcji wybierz maskowanie** . Kliknij pozycję **Dodaj**.
                :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png" alt-text="Utwórz zasady" border="true":::
1. Ponownie Wyświetl zawartość tabeli. Po zastosowaniu zasad Ranger można zobaczyć tylko cztery ostatnie znaki w kolumnie.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png" alt-text="tabela demonstracyjna po zastosowaniu zasad Ranger" border="true":::

## <a name="next-steps"></a>Następne kroki

* [Operacje biblioteki HWC i platformy Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Używanie zapytań interakcyjnych w usłudze HDInsight](./apache-interactive-query-get-started.md)
* [Integracja biblioteki HWC z rozwiązaniem Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [Przykłady współpracy z łącznikiem magazynu Hive przy użyciu Zeppelin, usługi Livy, Spark-Submit i pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
