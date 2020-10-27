---
title: Najlepsze praktyki dotyczące zarządzania klastrami — Azure HDInsight
description: Poznaj najlepsze rozwiązania dotyczące zarządzania klastrami usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fc2f9e5408dd760b48fc63c4021230446fe40de9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542322"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Najlepsze rozwiązania dotyczące zarządzania klastrem usługi HDInsight

Poznaj najlepsze rozwiązania dotyczące zarządzania klastrami usługi HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Jak mogę utworzyć klastry usługi HDInsight?

| Opcja | Dokumenty |
|---|---|
| Azure Data Factory | [Tworzenie klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu usługi Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Niestandardowy szablon Menedżer zasobów | [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Menedżer zasobów](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Szablony szybkiego startu | [Szablony szybkiego startu usługi HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| przykładów dla platformy Azure | [Przykłady dla platformy Azure dla usługi HDInsight](/samples/browse/?products=azure-hdinsight) |
| Azure Portal | [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal](./spark/apache-spark-intellij-tool-plugin.md) |
| Interfejs wiersza polecenia platformy Azure | [Tworzenie klastrów usługi HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Tworzenie klastrów Apache Hadoop przy użyciu interfejsu API REST platformy Azure](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| Zestawy SDK (.NET, Python, Java) | [.NET](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true), [Python](/python/api/overview/azure/hdinsight?preserve-view=true&view=azure-python), [Java](/java/api/overview/azure/hdinsight?preserve-view=true&view=azure-java-stable), [Przejdź](./hdinsight-go-sdk-overview.md) |

> [!Note]
> W przypadku tworzenia klastra i ponownego użycia nazwy klastra z wcześniej utworzonego klastra poczekaj na ukończenie poprzedniego usuwania klastra przed utworzeniem klastra.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Jak mogę dostosować klastry usługi HDInsight?

| Opcja | Dokumenty |
|---|---|
| Akcje skryptu | [Dostosowywanie klastrów usługi Azure HDInsight za pomocą akcji skryptu](./hdinsight-hadoop-customize-cluster-linux.md) |
| Uruchomienie | [Dostosowywanie klastrów usługi HDInsight przy użyciu narzędzia Bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Zewnętrzne magazyny metadanych | [Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Niestandardowa baza danych Ambari | [Konfigurowanie klastrów usługi HDInsight za pomocą niestandardowej bazy danych Ambari](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Jakie błędy mogą wystąpić podczas tworzenia klastrów?

| Błąd | Więcej informacji |
|---|---|
| Brak limitu przydziału | Istnieją przydziały dla liczby przydziałów, które można utworzyć w ramach subskrypcji w poszczególnych regionach. Aby uzyskać więcej informacji, zobacz [Planowanie pojemności: przydziały](./hdinsight-capacity-planning.md). |
| Nie ma więcej dostępnych adresów IP | Każda sieć wirtualna ma ograniczoną liczbę adresów IP. Podczas tworzenia klastra usługi HDInsight każdy węzeł (w tym węzły dozorcy i Gateway) używa niektórych z tych przydzielonych adresów IP. Jeśli wszystkie adresy IP są używane, ten błąd wystąpi.  |
| Reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) nie zezwalają na komunikację z dostawcami zasobów usługi HDInsight | Jeśli używasz sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika (UDR) do kontrolowania ruchu przychodzącego do klastra usługi HDInsight, musisz się upewnić, że klaster może komunikować się z krytycznymi usługami Azure Health i Management. Aby uzyskać więcej informacji, zobacz [Tagi usług sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dla usługi Azure HDInsight](./hdinsight-service-tags.md) |
| Ponowne użycie nazwy klastra | Jeśli używasz wcześniej używanej nazwy klastra, musisz poczekać X liczbę minut przed ponownym utworzeniem klastra. W przeciwnym razie zostanie wyświetlony komunikat o tym, że zasób już istnieje. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Jak mogę zarządzać uruchomionymi klastrami usługi HDInsight?

| Opcja | Dokumenty |
|---|---|
| Automatyczne skalowanie | [Automatyczne skalowanie klastrów usługi Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Skalowanie ręczne | [Skalowanie klastrów usługi Azure HDInsight](./hdinsight-scaling-best-practices.md) |
| Monitorowanie za pomocą Ambari| [Monitorowanie wydajności klastra w usłudze Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Monitorowanie za pomocą dzienników Azure Monitor | [Monitorowanie klastrów usługi HDInsight za pomocą dzienników usługi Azure Monitor](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |
| Problemy z usługą, planowana konserwacja, usługi kondycji & Security Advisor | [Subskrybowanie alertów dotyczących kondycji usługi dla subskrypcji](../service-health/alerts-activity-log-service-notifications-portal.md) |


## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Jak mogę sprawdzić usuniętych klastrów usługi HDInsight?

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Do monitorowania usuniętych klastrów można użyć następującego zapytania z dziennikami Azure Monitor.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Następne kroki

* [Planowanie wydajności klastrów usługi HDInsight](./hdinsight-capacity-planning.md)
* [Jakie są domyślne i zalecane konfiguracje węzłów dla usługi Azure HDInsight?](./hdinsight-supported-node-configuration.md)