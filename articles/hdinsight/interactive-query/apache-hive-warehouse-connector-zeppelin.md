---
title: Łącznik magazynu Hive — Apache Zeppelin przy użyciu usługi Livy — Azure HDInsight
description: Dowiedz się, jak zintegrować łącznik magazynu Hive z platformą Apache Zeppelin w usłudze Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 7957041c9d445f4239b8f0100128a531d982b451
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871558"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrowanie platformy Apache Zeppelin z łącznikiem magazynu Hive w usłudze Azure HDInsight

Klastry usługi HDInsight Spark obejmują notesy Apache Zeppelin z różnymi interpreterami. W tym artykule skupmy się tylko na interpreterze usługi Livy, aby uzyskać dostęp do tabel Hive z platformy Spark przy użyciu łącznika magazynu Hive.

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku *dozwolonych*, termin, przez który firma Microsoft już nie używa. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

## <a name="prerequisite"></a>Wymaganie wstępne

Ukończ kroki [konfiguracji łącznika magazynu Hive](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) .

## <a name="getting-started"></a>Wprowadzenie

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) , aby nawiązać połączenie z klastrem Apache Spark. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. W sesji SSH wykonaj następujące polecenie, aby zanotować wersje dla systemów `hive-warehouse-connector-assembly` i `pyspark_hwc` :

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Zapisz dane wyjściowe do późniejszego użycia podczas konfigurowania platformy Apache Zeppelin.

## <a name="configure-livy"></a>Konfigurowanie usługi Livy

Do uzyskiwania dostępu do tabel programu Hive z Zeppelin za pomocą interpretera usługi Livy są wymagane następujące konfiguracje.

### <a name="interactive-query-cluster"></a>Interaktywny klaster zapytań

1. W przeglądarce sieci Web przejdź do `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` lokalizacji, gdzie LLAPCLUSTERNAME jest nazwą klastra zapytań interaktywnych.

1. Przejdź do **zaawansowanej**  >  **niestandardowej lokacji głównej**. Wybierz pozycję **Dodaj właściwość...** , aby dodać następujące konfiguracje:

    | Konfigurowanie                 | Wartość |
    | ----------------------------- |-------|
    | Hadoop. PROXYUSER. usługi Livy. Groups  | *     |
    | Hadoop. PROXYUSER. usługi Livy. hosty   | *     |

1. Zapisz zmiany i ponownie uruchom wszystkie składniki, których to dotyczy.

### <a name="spark-cluster"></a>Klaster Spark

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` lokalizacji, gdzie ClusterName jest nazwą klastra Apache Spark.

1. Rozwiń pozycję **Custom livy2-conf**. Wybierz pozycję **Dodaj właściwość...** , aby dodać następującą konfigurację:

    | Konfigurowanie                 | Wartość                                      |
    | ----------------------------- |------------------------------------------  |
    | usługi Livy. plik. Local-dir-dozwolonych | /usr/HDP/Current/hive_warehouse_connector/ |

1. Zapisz zmiany i ponownie uruchom wszystkie składniki, których to dotyczy.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Konfigurowanie interpretera usługi Livy w interfejsie użytkownika Zeppelin (klaster Spark)

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra Apache Spark.

1. Przejdź do **livy2**.

1. Dodaj następujące konfiguracje:

    | Konfigurowanie                 | Wartość                                      |
    | ----------------------------- |:------------------------------------------:|
    | usługi Livy. Spark. Hadoop. Hive. llap. Demon. Service. hosts | @llap0 |
    | usługi Livy. Spark. Security. Credentials. serwera hiveserver2. Enabled | true |
    | usługi Livy. Spark. SQL. Hive. llap | true |
    | usługi Livy. Spark. przędz. Security. Credentials. serwera hiveserver2. Enabled | true |
    | usługi Livy. moje użytkownika | usługi Livy, Zeppelin |
    | usługi Livy. Spark. Jars | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Zastąp wersję wartością uzyskaną od [początku](#getting-started). |
    | usługi Livy. Spark. Submit. pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Zastąp wersję wartością uzyskaną od [początku](#getting-started). |
    | usługi Livy. Spark. SQL. Hive. serwera hiveserver2. JDBC. URL | Ustaw ją na adres URL serwera hiveserver2 Interactive JDBC klastra zapytań interaktywnych. |
    | Spark. Security. Credentials. serwera hiveserver2. Enabled | true |

1. W przypadku klastrów ESP Dodaj następującą konfigurację:

    | Konfigurowanie| Wartość|
    |---|---|
    | usługi Livy. Spark. SQL. Hive. serwera hiveserver2. JDBC. URL. Principal | `hive/<llap-headnode>@<AAD-Domain>` |

    * W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` lokalizacji, gdzie ClusterName jest nazwą klastra interakcyjnych zapytań. Kliknij pozycję **serwera hiveserver2 Interactive**. Zostanie wyświetlona w pełni kwalifikowana nazwa domeny (FQDN) węzła głównego, na którym działa LLAP, jak pokazano na zrzucie ekranu. Zamień `<llap-headnode>` na tę wartość.

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="Węzeł główny łącznika magazynu Hive" border="true":::

    * Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem interakcyjnych zapytań. Wyszukaj `default_realm` parametr w `/etc/krb5.conf` pliku. Zamień `<AAD-DOMAIN>` na tę wartość jako ciąg pisany wielkimi literami, w przeciwnym razie nie znaleziono poświadczeń.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="Domena usługi AAD magazynu usługi Hive" border="true":::

    * Na przykład `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .

1. Zapisz zmiany i ponownie uruchom interpreter usługi Livy.

Jeśli interpreter usługi Livy jest niedostępny, zmodyfikuj `shiro.ini` plik obecny w składniku Zeppelin w Ambari. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zabezpieczeń Apache Zeppelin](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html).  


## <a name="running-queries-in-zeppelin"></a>Uruchamianie zapytań w Zeppelin 

Uruchom Notes Zeppelin przy użyciu interpretera usługi Livy i wykonaj następujące czynności

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>Następne kroki

* [Operacje biblioteki HWC i platformy Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Integracja biblioteki HWC z platformą Apache Spark i programem Apache Hive](./apache-hive-warehouse-connector.md)
* [Używanie zapytań interakcyjnych w usłudze HDInsight](./apache-interactive-query-get-started.md)
