---
title: Porty używane przez usługi Hadoop w usłudze HDInsight — Azure
description: Ten artykuł zawiera listę portów używanych przez usługi Apache Hadoop Services uruchomionych w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: b5dadb886078f701bb01447efd606c862ee73073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945522"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Porty używane przez usługi Apache Hadoop w usłudze HDInsight

Ten dokument zawiera listę portów używanych przez Apache Hadoop usług uruchomionych w ramach klastrów usługi HDInsight. Zawiera również informacje o portach używanych do łączenia się z klastrem przy użyciu protokołu SSH.

## <a name="public-ports-vs-non-public-ports"></a>Porty publiczne a porty inne niż publiczne

Klastry usługi HDInsight oparte na systemie Linux ujawniają tylko trzy porty publicznie w Internecie: 22, 23 i 443. Te porty zapewniają bezpieczny dostęp do klastra przy użyciu protokołów SSH i usług udostępnianych za pośrednictwem protokołu Secure HTTPS.

Usługa HDInsight jest implementowana przez kilka Virtual Machines platformy Azure (węzłów klastra) uruchomionych w usłudze Azure Virtual Network. Z poziomu sieci wirtualnej można uzyskać dostęp do portów, które nie są udostępniane przez Internet. Jeśli łączysz się za pośrednictwem protokołu SSH z węzłem głównym, możesz bezpośrednio uzyskać dostęp do usług uruchomionych w węzłach klastra.

> [!IMPORTANT]  
> Jeśli nie określisz Virtual Network platformy Azure jako opcji konfiguracji usługi HDInsight, zostanie ona utworzona automatycznie. Nie można jednak dołączyć do tej sieci wirtualnej innych maszyn (takich jak inne Virtual Machines platformy Azure lub na komputerze deweloperskim klienta).

Aby dołączyć dodatkowe maszyny do sieci wirtualnej, należy najpierw utworzyć sieć wirtualną, a następnie określić ją podczas tworzenia klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Porty publiczne

Wszystkie węzły w klastrze usługi HDInsight znajdują się w Virtual Network platformy Azure. Nie można uzyskać dostępu do tych węzłów bezpośrednio z Internetu. Brama publiczna zapewnia dostęp do Internetu do następujących portów, które są wspólne dla wszystkich typów klastrów usługi HDInsight.

| Usługa | Port | Protokół | Opis |
| --- | --- | --- | --- |
| SSHD |22 |Protokół SSH |Łączy klientów z usługą SSHD na podstawowym węzła głównego. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| SSHD |22 |Protokół SSH |Łączy klientów z sshdą w węźle brzegowym. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| SSHD |23 |Protokół SSH |Łączy klientów z usługą SSHD na pomocniczym węzła głównego. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Interfejs użytkownika sieci Web Ambari. Zobacz [Zarządzanie usługą HDInsight za pomocą interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Interfejs API REST usługi Ambari. Zobacz [Zarządzanie usługą HDInsight przy użyciu interfejsu API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |Interfejs API REST usługi HCatalog. Zobacz  [Używanie MapReduce z zwinięciem](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| Serwera hiveserver2 |443 |ODBC |Nawiązuje połączenie z programem Hive przy użyciu ODBC. Zobacz [łączenie programu Excel z usługą HDInsight za pomocą sterownika ODBC firmy Microsoft](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| Serwera hiveserver2 |443 |JDBC |Nawiązuje połączenie z usługą ApacheHive przy użyciu JDBC. Zobacz [nawiązywanie połączenia Apache Hive w usłudze HDInsight przy użyciu sterownika Hive JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Dla określonych typów klastrów dostępne są następujące elementy:

| Usługa | Port | Protokół | Typ klastra | Opis |
| --- | --- | --- | --- | --- |
| `Stargate` |443 |HTTPS |HBase |Interfejs API REST usługi HBase. Zobacz Rozpoczynanie [korzystania z usługi Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |platforma Spark |Interfejs API REST platformy Spark. Zobacz [przesyłanie zadań Apache Spark zdalnie przy użyciu oprogramowania Apache usługi Livy](spark/apache-spark-livy-rest-interface.md) |
| Serwer Spark Thrift |443 |HTTPS |platforma Spark |Serwer Spark Thrift używany do przesyłania zapytań Hive. Zobacz [Używanie z usługi Beeline z usługą Apache Hive w usłudze HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Interfejs użytkownika sieci Web burzy. Zobacz [wdrażanie topologii Apache Storm w usłudze HDInsight i zarządzanie nimi](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Serwer proxy REST Kafka |443 |HTTPS |Kafka |Interfejs API REST usługi Kafka. Zobacz [posługiwanie się klastrami Apache Kafka w usłudze Azure HDInsight przy użyciu serwera proxy REST](kafka/rest-proxy.md) |

### <a name="authentication"></a>Authentication

Wszystkie usługi publicznie uwidocznione w Internecie muszą zostać uwierzytelnione:

| Port | Poświadczenia |
| --- | --- |
| 22 lub 23 |Poświadczenia użytkownika SSH określone podczas tworzenia klastra |
| 443 |Nazwa logowania (domyślnie: Administrator) i hasło, które zostały ustawione podczas tworzenia klastra |

## <a name="non-public-ports"></a>Porty inne niż publiczne

> [!NOTE]  
> Niektóre usługi są dostępne tylko dla określonych typów klastrów. Na przykład HBase jest dostępna tylko w przypadku typów klastrów HBase.

> [!IMPORTANT]  
> Niektóre usługi są uruchamiane tylko na jednym węzła głównego w danym momencie. Jeśli podjęto próbę nawiązania połączenia z usługą w głównym węzła głównego i pojawieniu się błędu, spróbuj ponownie za pomocą pomocniczej węzła głównego.

### <a name="ambari"></a>Ambari

| Usługa | Węzły | Port | Ścieżka adresu URL | Protokół |
| --- | --- | --- | --- | --- |
| Interfejs użytkownika sieci Web Ambari | Węzły główne | 8080 | / | HTTP |
| Interfejs API REST usługi Ambari | Węzły główne | 8080 | /api/v1 | HTTP |

Przykłady:

* Interfejs API REST usługi Ambari: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Porty HDFS

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Interfejs użytkownika sieci Web NameNode |Węzły główne |30070 |HTTPS |Interfejs użytkownika sieci Web do wyświetlania stanu |
| Usługa metadanych NameNode |węzły główne |8020 |IPC |Metadane systemu plików |
| Węzeł datanode |Wszystkie węzły procesu roboczego |30075 |HTTPS |Interfejs użytkownika sieci Web służący do wyświetlania stanu, dzienników i tak dalej. |
| Węzeł datanode |Wszystkie węzły procesu roboczego |30010 |&nbsp; |Transfer danych |
| Węzeł datanode |Wszystkie węzły procesu roboczego |30020 |IPC |Operacje na metadanych |
| Pomocniczy NameNode |Węzły główne |50090 |HTTP |Punkt kontrolny dla metadanych NameNode |

### <a name="yarn-ports"></a>Porty PRZĘDZy

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Interfejs użytkownika sieci Web Menedżer zasobów |Węzły główne |8088 |HTTP |Interfejs użytkownika sieci Web dla Menedżer zasobów |
| Interfejs użytkownika sieci Web Menedżer zasobów |Węzły główne |8090 |HTTPS |Interfejs użytkownika sieci Web dla Menedżer zasobów |
| Interfejs administratora Menedżer zasobów |węzły główne |8141 |IPC |W przypadku przesłanych aplikacji (Hive, serwer Hive, świnie itd.) |
| Harmonogram Menedżer zasobów |węzły główne |8030 |HTTP |Interfejs administracyjny |
| Interfejs aplikacji Menedżer zasobów |węzły główne |8050 |HTTP |Adres interfejsu Menedżera aplikacji |
| Węzełmanager |Wszystkie węzły procesu roboczego |30050 |&nbsp; |Adres menedżera kontenera |
| Interfejs użytkownika sieci Web nodemanager |Wszystkie węzły procesu roboczego |30060 |HTTP |Interfejs Menedżer zasobów |
| Adres osi czasu |Węzły główne |10200 |Zdalne wywołanie procedury |Usługa zdalnego wywoływania procedur usługi RPC. |
| Interfejs użytkownika sieci Web osi czasu |Węzły główne |8188 |HTTP |Interfejs użytkownika sieci Web usługi oś czasu |

### <a name="hive-ports"></a>Porty Hive

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Serwera hiveserver2 |Węzły główne |10001 |Thrift |Usługa do łączenia z usługą Hive (Thrift/JDBC) |
| Magazyn metadanych Hive |Węzły główne |9083 |Thrift |Usługa do łączenia z metadanymi programu Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Porty WebHCat

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Serwer WebHCat |Węzły główne |30111 |HTTP |Interfejs API sieci Web na platformie HCatalog i innych usług Hadoop |

### <a name="mapreduce-ports"></a>Porty MapReduce

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| JobHistory |Węzły główne |19888 |HTTP |Interfejs użytkownika sieci Web MapReduce JobHistory |
| JobHistory |Węzły główne |10020 |&nbsp; |Serwer MapReduce JobHistory |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Przesyła dane wyjściowe z mapy pośredniej do żądania środków ograniczających |

### <a name="oozie"></a>Oozie

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Serwer Oozie |Węzły główne |11000 |HTTP |Adres URL usługi Oozie |
| Serwer Oozie |Węzły główne |11001 |HTTP |Port dla administratora Oozie |

### <a name="ambari-metrics"></a>Metryki systemu Ambari

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Oś czasu (historia aplikacji) |Węzły główne |6188 |HTTP |Interfejs użytkownika sieci Web usługi oś czasu |
| Oś czasu (historia aplikacji) |Węzły główne |30200 |Zdalne wywołanie procedury |Interfejs użytkownika sieci Web usługi oś czasu |

### <a name="hbase-ports"></a>Porty HBase

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Serwera hmaster |Węzły główne |16000 |&nbsp; |&nbsp; |
| Interfejs użytkownika sieci Web info serwera hmaster |Węzły główne |16010 |HTTP |Port internetowego interfejsu użytkownika HBase Master |
| Serwer regionu |Wszystkie węzły procesu roboczego |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Port używany przez klientów do łączenia się z usługą dozorcy |

### <a name="kafka-ports"></a>Porty Kafka

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Brokera |Węzły procesu roboczego |9092 |Protokół sieci Kafka |Używany do komunikacji z klientem |
| &nbsp; |Dozorcy węzły |2181 |&nbsp; |Port używany przez klientów do łączenia się z usługą dozorcy |
| Serwer proxy REST | Węzły zarządzania Kafka |9400 |HTTPS |[Kafka — Specyfikacja REST](/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Porty platformy Spark

| Usługa | Węzły | Port | Protokół | Ścieżka adresu URL | Opis |
| --- | --- | --- | --- | --- | --- |
| Serwery Spark Thrift |Węzły główne |10002 |Thrift | &nbsp; | Usługa do łączenia z platformą Spark SQL (Thrift/JDBC) |
| Serwer usługi Livy | Węzły główne | 8998 | HTTP | &nbsp; | Usługa do uruchamiania instrukcji, zadań i aplikacji |
| Notes Jupyter | Węzły główne | 8001 | HTTP | &nbsp; | Witryna sieci Web Jupyter Notebook |

Przykłady:

* Usługi Livy: `curl -u admin -G "http://10.0.0.11:8998/"` . W tym przykładzie `10.0.0.11` jest adresem IP węzła głównego, który hostuje usługę usługi Livy.