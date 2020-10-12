---
title: Apache Hadoop składniki i wersje — Azure HDInsight
description: Dowiedz się więcej o składnikach i wersjach Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: 15d7b11ed49880a81e52c48a93a72e1491a5b645
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394333"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Składniki i wersje oprogramowania Apache dostępne dla usługi Azure HDInsight

Ten artykuł zawiera informacje o składnikach i wersjach środowiska [Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight i pakiet Enterprise Security. Dowiesz się również, jak sprawdzić wersje składników usługi Hadoop w usłudze HDInsight.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Składniki Apache dostępne z różnymi wersjami usługi HDInsight

Usługa Azure HDInsight obsługuje wiele wersji klastra Hadoop, które można wdrożyć w dowolnym momencie. 4 kwietnia 2017 domyślna wersja klastra używana przez usługę Azure HDInsight to 3,6.

Wersje składników skojarzone z wersjami klastra usługi HDInsight są wymienione w poniższej tabeli.

> [!NOTE]
> Domyślna wersja usługi HDInsight może ulec zmianie bez powiadomienia. Jeśli masz zależność wersji, określ wersję usługi HDInsight podczas tworzenia klastrów przy użyciu zestawu .NET SDK z Azure PowerShell oraz klasycznego interfejsu wiersza polecenia platformy Azure.

| Składnik              | HDInsight 4.0 | HDInsight 3,6 (domyślnie)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop i PRZĘDZa | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache świni             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 on w zapytaniu Interactive ESP) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache dozorcy       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0 +                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | 2.3.2.                      |
| Apache Livy            | 0,5           | 0,4.                        |
| Apache Kafka           | 2.1.1         | 1,1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4,0: Spark 2,4 i Kafka 2,1 są w pełni obsługiwane. Jednak typy klastrów Spark 2,3 i Kafka 1,1 nie są obsługiwane. HDInsight 3,6: Spark 2,3 i Kafka 1,1 są w pełni obsługiwane.  

## <a name="check-for-current-apache-component-version-information"></a>Wyszukaj bieżące informacje o wersji składnika Apache

Wersje składników środowiska Hadoop skojarzone z wersjami klastra usługi HDInsight mogą ulec zmianie przy użyciu aktualizacji usługi HDInsight. Aby sprawdzić składniki usługi Hadoop i sprawdzić, które wersje są używane dla klastra, użyj interfejsu API REST Ambari. Polecenie **GetComponentInformation** pobiera informacje o składnikach usługi. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Informacje o wersji

Aby uzyskać dodatkowe informacje o wersji dotyczące najnowszych wersji usługi HDInsight, zobacz [Informacje o wersji usługi HDInsight](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Obsługiwane wersje usługi HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Obsługa wygaśnięcia i wycofania wersji usługi HDInsight

**Wygaśnięcie pomocy technicznej** oznacza, że firma Microsoft nie zapewnia już pomocy technicznej dotyczącej określonej wersji usługi HDInsight. Nie jest już dostępny w Azure Portal na potrzeby tworzenia klastra. Te wersje można nadal tworzyć przy użyciu interfejsu wiersza polecenia platformy Azure lub różnych zestawów SDK.

**Wycofanie** oznacza, że istniejące klastry wersji usługi HDInsight nadal działają zgodnie z oczekiwaniami. Nowych klastrów tej wersji nie można utworzyć za pomocą żadnych metod, które obejmują interfejs wiersza polecenia i zestawy SDK. Inne funkcje płaszczyzny kontroli, takie jak skalowanie ręczne i skalowanie automatyczne, mogą nie funkcjonować po wycofaniu wersji. Pomoc techniczna nie jest dostępna dla wycofanych wersji.

W poniższych tabelach przedstawiono wersje usługi HDInsight. Daty wygaśnięcia i wycofania pomocy technicznej są również udostępniane, gdy są znane.

### <a name="available-versions"></a>Dostępne wersje

Ta tabela zawiera listę wersji usługi HDInsight dostępnych w Azure Portal i innych metod wdrażania, takich jak program PowerShell i zestaw .NET SDK.

| Wersja usługi HDInsight | System operacyjny maszyny wirtualnej | Data wydania | Data wygaśnięcia pomocy technicznej | Data wycofania | Wysoka dostępność |  Dostępność w Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |Wrzesień 24, 2018 | | |Tak |Tak |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |4 kwietnia 2017      | * 30 czerwca 2021 |30 czerwca 2021 |Tak |Tak |

* Rozszerzamy przedział czasu pomocy technicznej dla niektórych typów klastrów usługi HDInsight 3,6

| Typ klastra                    | Wersja struktury | Bieżące wygaśnięcie pomocy technicznej        | Nowa Data wygaśnięcia obsługi |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| Usługa HDInsight 3,6 Hadoop            | 2.7.3             | 31 grudnia 2020                      | 30 czerwca 2021               |
| HDInsight 3,6 Spark             | 2.3               | 31 grudnia 2020                      | 30 czerwca 2021               |
| HDInsight 3,6 Spark             | 2.2               | Wycofano 30 czerwca 2020          |                             |
| HDInsight 3,6 Spark             | 2.1               | Wycofano 30 czerwca 2020          |                             |
| HDInsight 3,6 Kafka             | 1,1               | 31 grudnia 2020                      | 30 czerwca 2021               |
| HDInsight 3,6 Kafka             | 1.0               | Wycofano 30 czerwca 2020.         |                             |
| HDInsight 3,6 HBase             | 1,1               | 31 grudnia 2020                      | 30 czerwca 2021               |
| Zapytanie interaktywne usługi HDInsight 3,6 | 2.1               | 31 grudnia 2020                      | 30 czerwca 2021               |
| Usługa HDInsight 3,6             | 1,1               | 31 grudnia 2020                      | 30 czerwca 2021               |
| Usługi HDInsight 3,6 ML      | 9,3               | 31 grudnia 2020                      | 31 grudnia 2020                |

> [!NOTE]
> Po wygaśnięciu obsługi wersji klastra może ona nie być dostępna za pomocą Azure Portal. W niektórych przypadkach wersje klastra są nadal dostępne za pomocą parametru **Version** w poleceniu [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) programu Windows PowerShell i zestawu .NET SDK do momentu wycofania wersji.

### <a name="retired-versions"></a>Wycofane wersje

W tej tabeli wymieniono wersje usługi HDInsight, które nie są dostępne w Azure Portal.

| Wersja usługi HDInsight | Wersja HDP | System operacyjny maszyny wirtualnej | Data wydania | Data wygaśnięcia pomocy technicznej | Data wycofania | Wysoka dostępność |  Dostępność w Azure Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |30 września 2016 |5 września 2017 |28 czerwca 2018 |Tak |Nie |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4 LTS |29 marca 2016 |29 grudnia 2016 |9 stycznia 2018 r. |Tak |Nie |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 z dodatkiem R2 |2 grudnia 2015 |27 czerwca 2016 r. |31 lipca 2018 r. |Tak |Nie |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2 grudnia 2015 |27 czerwca 2016 r. |31 lipca 2017 |Tak |Nie |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12,04 LTS lub Windows Server 2012 R2 |18 lutego 2015 |1 marca 2016 |1 kwietnia 2017 r. |Tak |Nie |
| HDInsight 3,1 |HDP 2,1 |Windows Server 2012 z dodatkiem R2 |24 czerwca 2014 |18 maja 2015 |30 czerwca 2016 |Tak |Nie |
| HDInsight 3,0 |HDP 2,0 |Windows Server 2012 z dodatkiem R2 |11 lutego 2014 |17 września 2014 |30 czerwca 2015 |Tak |Nie |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 z dodatkiem R2 |28 października 2013 |12 maja 2014 |31 maja 2015 |Tak |Nie |
| HDInsight 1,6 |HDP 1,1 | |28 października 2013 |26 kwietnia 2014 |31 maja 2015 |Nie |Nie |

> [!NOTE]
> Klastry o wysokiej dostępności z dwoma węzłami głównymi są wdrażane domyślnie dla usługi HDInsight w wersji 2,1 lub nowszej. Nie są one dostępne w klastrach usługi HDInsight w wersji 1,6.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Umowa dotycząca poziomu usług dla wersji klastra usługi HDInsight

Umowa dotycząca poziomu usług jest definiowana w _oknie obsługi_. Okno pomocy technicznej to czas, przez który wersja usługi HDInsight jest obsługiwana przez dział obsługi klienta firmy Microsoft. Jeśli wersja została przeniesiona na _datę wygaśnięcia obsługi_, klaster usługi HDInsight jest poza oknem obsługi. Obsługa wygasania w usłudze HDInsight w wersji X (dostępna jest nowsza wersja X + 1) jest nowsza:

- **Wzór 1:** Dodaj 180 dni do daty wydania klastra usługi HDInsight w wersji X.
- **Formuła 2:** Dodaj 90 dni do daty, w której w Azure Portal jest dostępny klaster usługi HDInsight w wersji X + 1.

_Data wycofania_ to data, po której nie można utworzyć wersji klastra w usłudze HDInsight. Od 31 lipca 2017 nie można zmienić rozmiaru klastra usługi HDInsight po jego dacie wycofania.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Domyślna konfiguracja węzła i rozmiary maszyn wirtualnych dla klastrów

Aby uzyskać więcej informacji na temat jednostek SKU maszyn wirtualnych do wybrania dla klastra, zobacz [szczegóły konfiguracji klastra usługi Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Następne kroki

- [Konfiguracja klastra dla Apache Hadoop, Spark i nie tylko w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Pracuj w Apache Hadoop w usłudze HDInsight z komputera z systemem Windows](hdinsight-hadoop-windows-tools.md)
- [Informacje o wersji Hortonworks skojarzone z wersjami usługi Azure HDInsight](./hortonworks-release-notes.md)
- [Pakiet Enterprise Security](./enterprise-security-package.md)
