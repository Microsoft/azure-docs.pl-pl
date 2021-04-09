---
title: Apache Hadoop składniki i wersje — Azure HDInsight 3,6
description: Dowiedz się więcej o składnikach i wersjach Apache Hadoop w usłudze Azure HDInsight 3,6.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 32b287c3d7b1974db5a079d1ee84aaafad3faed7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727692"
---
# <a name="hdinsight-36-component-versions"></a>Wersje składników usługi HDInsight 3,6

W tym artykule omówiono składniki i wersje środowiska Apache Hadoop w usłudze Azure HDInsight 3,6.

## <a name="support-for-hdinsight-36"></a>Obsługa usługi HDInsight 3,6

Od 1 lipca 2021 firma Microsoft będzie oferować pomoc techniczna Basic dla niektórych typów klastrów HDI 3,6.
Poniższa tabela zawiera listę przedziałów czasu pomocy technicznej dla typów klastrów usługi HDInsight 3,6.

| Typ klastra                    | Wersja struktury | Wygaśnięcie pomocy technicznej Standard       | Data wygaśnięcia pomoc techniczna Basic | Data wycofania |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| Usługa HDInsight 3,6 Hadoop            | 2.7.3             | 30 czerwca 2021                     | 3 kwietnia 2022                | 4 kwietnia 2022 |
| HDInsight 3,6 Spark             | 2.3               | 30 czerwca 2021                     | 3 kwietnia 2022                | 4 kwietnia 2022 |
| HDInsight 3,6 Kafka             | 1.1               | 30 czerwca 2021                     | 3 kwietnia 2022                | 4 kwietnia 2022 |
| HDInsight 3,6 HBase             | 1.1               | 30 czerwca 2021                     | 3 kwietnia 2022                | 4 kwietnia 2022 |
| Zapytanie interaktywne usługi HDInsight 3,6 | 2.1               | 30 czerwca 2021                     | 3 kwietnia 2022                | 4 kwietnia 2022 |
| Usługa HDInsight 3,6             | 1.1               | 30 czerwca 2021                     | 3 kwietnia 2022                | 4 kwietnia 2022 |
| Usługi HDInsight 3,6 ML      | 9,3               | -                                 | -                            | 31 grudnia 2020 |
| HDInsight 3,6 Spark             | 2,2               | -                                 | -                            | 30 czerwca 2020 r. |
| HDInsight 3,6 Spark             | 2.1               | -                                 | -                            | 30 czerwca 2020 r. |
| HDInsight 3,6 Kafka             | 1.0               | -                                 | -                            | 30 czerwca 2020 r. |

## <a name="apache-components-available-with-hdinsight-version-36"></a>Składniki Apache dostępne w usłudze HDInsight w wersji 3,6

Wersje składnika OSS skojarzone z usługą HDInsight 3,6 są wymienione w poniższej tabeli.

| Składnik              | HDInsight 3,6 (domyślnie)     |
|------------------------|-----------------------------|
| Apache Hadoop i PRZĘDZa | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache świni             | 0.16.0                      |
| Apache Hive            | (2.1.0 w zapytaniu Interactive ESP) |
| Apache Tez Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache dozorcy       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0 +                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0,4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>Następne kroki

- [Konfiguracja klastra dla Apache Hadoop, Spark i nie tylko w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Pakiet Enterprise Security](./enterprise-security-package.md)
- [Pracuj w Apache Hadoop w usłudze HDInsight z komputera z systemem Windows](hdinsight-hadoop-windows-tools.md)
