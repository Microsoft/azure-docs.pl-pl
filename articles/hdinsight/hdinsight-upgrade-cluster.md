---
title: Migrowanie klastra do nowszej wersji
titleSuffix: Azure HDInsight
description: Zapoznaj się z zaleceniami dotyczącymi migracji klastra usługi Azure HDInsight do nowszej wersji.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 04da5d668515fe96d50d4e6a7d0f5ff1c4c48c27
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931374"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrowanie klastra usługi HDInsight do nowszej wersji

Aby skorzystać z najnowszych funkcji usługi HDInsight, zalecamy regularne Migrowanie klastrów usługi HDInsight do najnowszej wersji. Usługa HDInsight nie obsługuje uaktualnień w miejscu, w których istniejący klaster jest uaktualniany do nowszej wersji składnika. Należy utworzyć nowy klaster z żądanym składnikiem i wersją platformy, a następnie przeprowadzić migrację aplikacji w celu korzystania z nowego klastra. Postępuj zgodnie z poniższymi wskazówkami, aby przeprowadzić migrację wersji klastra usługi HDInsight.

> [!NOTE]  
> Aby uzyskać informacje dotyczące obsługiwanych wersji usługi HDInsight, zobacz [wersje składników usługi HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Zadania migracji

Przepływ pracy uaktualniania klastra usługi HDInsight jest następujący:.
![Diagram przepływu pracy uaktualniania usługi HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Przeczytaj każdą z sekcji tego dokumentu, aby poznać zmiany, które mogą być wymagane podczas uaktualniania klastra usługi HDInsight.
2. Utwórz klaster jako środowisko testu/jakości. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [informacje na temat tworzenia klastrów usługi HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Skopiuj istniejące zadania, źródła danych i ujścia do nowego środowiska.
4. Wykonaj testy weryfikacyjne, aby upewnić się, że zadania działają zgodnie z oczekiwaniami w nowym klastrze.

Po zweryfikowaniu, że wszystko działa zgodnie z oczekiwaniami, Zaplanuj przestój dla migracji. W trakcie tego przestoju wykonaj następujące czynności:

1. Wykonaj kopię zapasową wszystkich danych przejściowych przechowywanych lokalnie w węzłach klastra. Na przykład, jeśli masz dane przechowywane bezpośrednio w węźle głównym.
1. [Usuń istniejący klaster](./hdinsight-delete-cluster.md).
1. Utwórz klaster w tej samej podsieci wirtualnej z najnowszą (lub obsługiwaną) wersją HDI przy użyciu tego samego domyślnego magazynu danych, który został użyty przez poprzedni klaster. Dzięki temu nowy klaster będzie kontynuował pracę nad istniejącymi danymi produkcyjnymi.
1. Zaimportuj wszystkie dane przejściowe, których kopię zapasową utworzono.
1. Uruchom zadania/Kontynuuj przetwarzanie przy użyciu nowego klastra.

## <a name="workload-specific-guidance"></a>Wskazówki dotyczące obciążenia

W poniższych dokumentach przedstawiono wskazówki dotyczące migrowania konkretnych obciążeń:

* [Migruj HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migruj Kafka](./kafka/migrate-versions.md)
* [Migrowanie programu Hive/zapytania interaktywnego](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Aby uzyskać więcej informacji na temat kopii zapasowych i przywracania bazy danych, zobacz [odzyskiwanie bazy danych w Azure SQL Database przy użyciu zautomatyzowanych kopii zapasowych bazy danych](../azure-sql/database/recovery-using-backups.md).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak tworzyć klastry usługi HDInsight oparte na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Łączenie się z usługą HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Zarządzanie klastrem opartym na systemie Linux przy użyciu usługi Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Informacje o wersji usługi HDInsight](./hdinsight-version-release.md)
