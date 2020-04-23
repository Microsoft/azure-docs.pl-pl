---
title: Apache Hadoop składniki i wersje — Azure HDInsight
description: Poznaj składniki i wersje Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 337d36d8f5e7a7b69b98de2f68ff372d4914f905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084774"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Jakie są komponenty i wersje Apache Hadoop z HDInsight?

Dowiedz się więcej o składnikach i wersjach środowiska [Apache Hadoop](https://hadoop.apache.org/) w programie Microsoft Azure HDInsight oraz pakiecie zabezpieczeń dla przedsiębiorstw. Dowiedz się również, jak sprawdzić wersje komponentów Hadoop w HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Komponenty Apache Hadoop dostępne w różnych wersjach HDInsight

Usługa Azure HDInsight obsługuje wiele wersji klastra Hadoop, które można wdrożyć w dowolnym momencie. W dniu 4 kwietnia 2017 r. domyślna wersja klastra używana przez usługę Azure HDInsight to 3.6.

Wersje składników skojarzone z wersjami klastra HDInsight są wymienione w poniższej tabeli:

> [!NOTE]  
> Domyślna wersja usługi HDInsight może ulec zmianie bez powiadomienia. Jeśli masz zależność wersji, określ wersję HDInsight podczas tworzenia klastrów za pomocą zestawu .NET SDK z platformą Azure PowerShell i klasyczną platformą Azure CLI.

| Składnik              | HDInsight 4.0 | HDInsight 3.6 (domyślnie)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop i Przędza | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Świnia Apache             | 0.16.0        | 0.16.0                      |
| Ul Apache            | 3.1.0         | 2.1.0 (klastry inne niż ESP), 1.2.1 (klastry ESP)                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (Patrz uwaga poniżej) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Ze względu na wydajność systemu obsługa platformy Kafka w wersji 0.10 wygasła w marcu 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Sprawdź aktualne informacje o wersji składnika Hadoop

Wersje składników środowiska Usługi Hadoop skojarzone z wersjami klastra HDInsight mogą ulec zmianie wraz z aktualizacjami usługi HDInsight. Aby sprawdzić składniki Hadoop i sprawdzić, które wersje są używane dla klastra, użyj interfejsu API AMbari REST. Polecenie **GetComponentInformation** pobiera informacje o składnikach usługi. Szczegółowe informacje można znaleźć w [dokumentacji Apache Ambari.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

### <a name="release-notes"></a>Informacje o wersji

Zobacz [informacje o wersji HDInsight,](hdinsight-release-notes.md) aby uzyskać dodatkowe informacje o wersji w najnowszych wersjach programu HDInsight.

## <a name="supported-hdinsight-versions"></a>Obsługiwane wersje HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Obsługa wygasania i wycofywania wersji hdinsight

**Wygaśnięcie pomocy technicznej** oznacza, że firma Microsoft nie będzie już zapewniać pomocy technicznej dla określonej wersji usługi HDInsight. I nie będzie już dostępna za pośrednictwem witryny Azure portal do tworzenia klastra. Jednak te wersje nadal można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure lub różnych zestawów SDK.

**Wycofanie** wersji HDInsight oznacza, że istniejące klastry będą nadal działać tak, jak jest. Jednak nowe klastry tej wersji nie można tworzyć za pomocą dowolnych środków (w tym cli i SDK). Inne funkcje płaszczyzny sterowania (takie jak ręczne skalowanie i skalowanie automatyczne) również mogą nie działać po przejściu na emeryturę wersji. Pomoc techniczna nie jest dostępna dla wycofanych wersji.

W poniższych tabelach przedstawiono wersje programu HDInsight. Daty wygaśnięcia pomocy technicznej i przejścia na emeryturę są również podane, gdy są one znane.

### <a name="available-versions"></a>Dostępne wersje

W poniższej tabeli wymieniono wersje usługi HDInsight, które są dostępne w witrynie Azure portal i inne metody wdrażania, takie jak PowerShell i .NET SDK.

| Wersja HDInsight | System operacyjny maszyny wirtualnej | Data wydania | Data wygaśnięcia pomocy technicznej | Data przejścia na emeryturę | Wysoka dostępność |  Dostępność w witrynie Azure portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |Wrzesień 24, 2018 | | |Yes |Yes |
| HdInsight 3.6 |Ubuntu 16.0.4 LTS |4 kwietnia 2017 r. | 31 grudnia 2020 r. |31 grudnia 2020 r. |Yes |Yes |

Pomoc techniczna platformy Spark 2.1, 2.2 & Kafka 1.0 wygaśnie 30 czerwca 2020 roku.

> [!NOTE]  
> Po wygaśnięciu pomocy technicznej dla wersji może nie być dostępna za pośrednictwem witryny Microsoft Azure portal. Jednak wersje klastra są nadal `Version` dostępne przy użyciu parametru w poleceniu Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) i .NET SDK do daty wycofania wersji.

### <a name="retired-versions"></a>Wycofane wersje

W poniższej tabeli wymieniono wersje usługi HDInsight, które **nie są** dostępne w witrynie Azure portal.

| Wersja HDInsight | Wersja HDP | System operacyjny maszyny wirtualnej | Data wydania | Data wygaśnięcia pomocy technicznej | Data przejścia na emeryturę | Wysoka dostępność |  Dostępność w witrynie Azure portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HdInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 września 2016 r. |5 września 2017 r. |28 czerwca 2018 r. |Yes |Nie |
| HdInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 marca 2016 r. |29 grudnia 2016 r. |9 stycznia 2018 r. |Yes |Nie |
| HdInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 grudnia 2015 r. |27 czerwca 2016 r. |31 lipca 2018 r. |Yes |Nie |
| HdInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 grudnia 2015 r. |27 czerwca 2016 r. |31 lipca 2017 r. |Yes |Nie |
| HdInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS lub Windows Server 2012 R2 |18 lutego 2015 r. |1 marca 2016 r. |1 kwietnia 2017 r. |Yes |Nie |
| HdInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 czerwca 2014 r. |18 maja 2015 r. |30 czerwca 2016 r. |Yes |Nie |
| HdInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 lutego 2014 r. |17 września 2014 r. |30 czerwca 2015 |Yes |Nie |
| HdInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 października 2013 r. |12 maja 2014 r. |31 maja 2015 r. |Yes |Nie |
| HdInsight 1.6 |HDP 1.1 | |28 października 2013 r. |26 kwietnia 2014 r. |31 maja 2015 r. |Nie |Nie |

> [!NOTE]  
> Klastry o wysokiej dostępności z dwoma węzłami głównymi są domyślnie wdrażane dla usługi HDInsight w wersji 2.1 i nowszej. Nie są one dostępne dla klastrów HDInsight w wersji 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Pakiet zabezpieczeń dla przedsiębiorstwa dla rozwiązania HDInsight

Enterprise Security to opcjonalny pakiet, który można dodać do klastra HDInsight w ramach tworzenia przepływu pracy klastra. Pakiet zabezpieczeń przedsiębiorstwa obsługuje:

- Integracja z usługą Active Directory w celu uwierzytelniania.

    W przeszłości utworzono klastry HDInsight z lokalnym użytkownikiem administratora i lokalnym użytkownikiem SSH. Lokalny administrator może uzyskać dostęp do wszystkich plików, folderów, tabel i kolumn.  Pakiet zabezpieczeń przedsiębiorstwa umożliwia włączenie kontroli dostępu opartej na rolach, integrując usługę HDInsight z usługą Active Directory. W tym lokalna usługa Active Directory, usługi domenowe Active Directory platformy Azure. Lub usługa Active Directory na maszynie wirtualnej IaaS. Administrator domeny w klastrze może przyznać użytkownikom używanie własnej nazwy użytkownika i hasła firmy (domeny).

    Aby uzyskać więcej informacji, zobacz:

    - [Wprowadzenie do zabezpieczeń Apache Hadoop z klastrami HDInsight przyłączonych do domeny](./domain-joined/hdinsight-security-overview.md)
    - [Planowanie klastrów Apache Hadoop przyłączonych do domeny platformy Azure w usłudze HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurowanie przyłączonego do domeny środowiska piaskownicy](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurowanie klastrów hdinsight przyłączonych do domeny przy użyciu usług domenowych Usługi domenowe Active Directory azure](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autoryzacja danych

  - Integracja z Apache Ranger do autoryzacji dla kolejek hive, Spark SQL i Yarn.
  - Kontrolę dostępu można ustawić w plikach i folderach.

    Aby uzyskać więcej informacji, zobacz:

  - [Konfigurowanie zasad gałęzi Apache w udziale usługi HDInsight przyłączonych do domeny](./domain-joined/apache-domain-joined-run-hive.md)

- Wyświetlanie dzienników inspekcji w celu monitorowania dostępu i skonfigurowanych zasad.

### <a name="supported-cluster-types"></a>Obsługiwane typy klastrów

Obecnie tylko następujące typy klastrów obsługują pakiet zabezpieczeń przedsiębiorstwa:

- Hadoop (tylko HDInsight 3.6)
- platforma Spark
- Kafka
- HBase
- Zapytanie interakcyjne

### <a name="support-for-azure-data-lake-storage"></a>Obsługa usługi Azure Data Lake Storage

Pakiet zabezpieczeń dla przedsiębiorstw obsługuje korzystanie z usługi Azure Data Lake Storage zarówno jako magazynu podstawowego, jak i magazynu dodatku.

### <a name="pricing-and-service-level-agreement-sla"></a>Umowa cenowa i umowa dotyczącą poziomu usług (SLA)

Aby uzyskać informacje na temat cen i umowy SLA pakietu zabezpieczeń przedsiębiorstwa, zobacz [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Umowa dotyczącej poziomu usług dla wersji klastra USŁUGI HDInsight

Umowa dotyczącej poziomu usług (SLA) jest definiowana jako _okno pomocy technicznej_. Okno pomocy technicznej to okres, w który `Microsoft Customer Service and Support`jest obsługiwana wersja HDInsight . Jeśli wersja ma _datę wygaśnięcia_przekazanej pomocy technicznej, klaster HDInsight znajduje się poza oknem pomocy technicznej. Wygaśnięcie obsługi hdinsight w wersji X (po udostępnieniu nowszej wersji X +1) jest późniejsza:  

- Formuła 1: Dodaj 180 dni do daty wydania wersji X klastra HDInsight.
- Formuła 2: Dodaj 90 dni do daty udostępnienia klastra HDInsight w wersji X+1 w witrynie Azure portal.

_Data wycofania_ to data, po której nie można utworzyć wersji klastra w programie HDInsight. Od 31 lipca 2017 r. nie można zmienić rozmiaru klastra HDInsight po dacie wycofania.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Domyślna konfiguracja węzłów i rozmiary maszyn wirtualnych dla klastrów

Aby uzyskać więcej informacji na temat jednostek SKU maszyny wirtualnej do wybrania dla klastra, zobacz [szczegóły konfiguracji klastra usługi Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Następne kroki

- [Konfiguracja klastra dla Apache Hadoop, Spark i innych na HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Praca w Apache Hadoop na HDInsight z komputera z systemem Windows](hdinsight-hadoop-windows-tools.md)
- [Informacje o wydaniu hortonworks skojarzone z wersjami usługi Azure HDInsight](./hortonworks-release-notes.md)
