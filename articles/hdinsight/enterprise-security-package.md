---
title: pakiet Enterprise Security usługi Azure HDInsight
description: Poznaj składniki pakiet Enterprise Security i wersje w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 91fa6a8da555d0b0cc79b262a83306c1f72aa68a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567099"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>pakiet Enterprise Security usługi Azure HDInsight

Zabezpieczenia przedsiębiorstwa to opcjonalny pakiet, który można dodać do klastra usługi HDInsight w ramach przepływu pracy tworzenia klastra. Pakiet Enterprise Security obsługuje:

* Integracja z usługą Active Directory na potrzeby uwierzytelniania.

    W przeszłości utworzono klastry usługi HDInsight z lokalnym użytkownikiem administracyjnym i lokalnym użytkownikiem SSH. Użytkownik lokalny administrator ma dostęp do wszystkich plików, folderów, tabel i kolumn.  Za pomocą pakiet Enterprise Security włączasz kontrolę dostępu opartą na rolach przez integrację usługi HDInsight z Azure Active Directory Domain Services.

    Aby uzyskać więcej informacji, zobacz:

    * [Wprowadzenie do Apache Hadoop zabezpieczeń z przyłączonymi do domeny klastrami usługi HDInsight](./domain-joined/hdinsight-security-overview.md)

    * [Planowanie klastrów Apache Hadoop przyłączonych do domeny platformy Azure w usłudze HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Konfigurowanie przyłączonego do domeny środowiska piaskownicy](./domain-joined/apache-domain-joined-configure.md)

    * [Konfigurowanie przyłączonych do domeny klastrów usługi HDInsight przy użyciu Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Autoryzacja danych

  * Integracja z platformą Apache Ranger na potrzeby autoryzacji dla kolejek Hive, Spark SQL i przędzy.
  * Można ustawić kontrolę dostępu dla plików i folderów.

    Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad Apache Hive w usłudze HDInsight przyłączonych do domeny](./domain-joined/apache-domain-joined-run-hive.md)

* Wyświetl dzienniki inspekcji, aby monitorować dostęp i skonfigurowane zasady.

## <a name="supported-cluster-types"></a>Obsługiwane typy klastrów

Obecnie tylko następujące typy klastrów obsługują pakiet Enterprise Security:

* Hadoop (tylko Usługa HDInsight 3,6)
* platforma Spark
* Kafka
* HBase
* Zapytanie interakcyjne

## <a name="support-for-azure-data-lake-storage"></a>Obsługa usługi Azure Data Lake Storage

Pakiet Enterprise Security obsługuje używanie Azure Data Lake Storage zarówno jako magazynu podstawowego, jak i do magazynu dodatków.

## <a name="pricing-and-service-level-agreement-sla"></a>Cennik i umowa dotycząca poziomu usług (SLA)

Aby uzyskać informacje o cenach i umowie SLA dla pakiet Enterprise Security, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Następne kroki

* [Konfiguracja klastra dla Apache Hadoop, Spark i nie tylko w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Pracuj w Apache Hadoop w usłudze HDInsight z komputera z systemem Windows](hdinsight-hadoop-windows-tools.md)
* [Informacje o wersji Hortonworks skojarzone z wersjami usługi Azure HDInsight](./hortonworks-release-notes.md)
* [Składniki platformy Apache w usłudze HDInsight](./hdinsight-component-versioning.md)
