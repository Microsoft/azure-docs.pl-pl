---
title: Używanie Azure Monitor do monitorowania Azure HDInsight klastrach
description: Dowiedz się, jak używać dzienników Azure Monitor do monitorowania zadań uruchomionych w klastrze usługi HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 2a81b25b08708a878fc8ff83cf19c643036b8f90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770331"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Monitorowanie klastrów usługi HDInsight za pomocą dzienników usługi Azure Monitor

Dowiedz się, jak włączyć dzienniki Azure Monitor w celu monitorowania operacji klastra Hadoop w umacie HDInsight. Oraz jak dodać rozwiązanie do monitorowania usługi HDInsight.

[Azure Monitor to](../azure-monitor/logs/log-query-overview.md) usługa Azure Monitor, która monitoruje środowiska w chmurze i lokalne. Monitorowanie ma na celu zachowanie ich dostępności i wydajności. Zbiera dane generowane przez zasoby w chmurze, środowiskach lokalnych i z innych narzędzi do monitorowania. Dane są używane do analizy wielu źródeł.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Log Analytics. Ten obszar roboczy można określić jako unikatowe środowisko Azure Monitor z własnym repozytorium danych, źródłami danych i rozwiązaniami. Aby uzyskać instrukcje, [zobacz Tworzenie obszaru roboczego usługi Log Analytics.](../azure-monitor/vm/quick-collect-azurevm.md#create-a-workspace)

* Klaster usługi Azure HDInsight. Obecnie można używać dzienników Azure Monitor z następującymi typami klastrów usługi HDInsight:

  * Hadoop
  * HBase
  * Zapytanie interakcyjne
  * Kafka
  * platforma Spark
  * Storm

  Aby uzyskać instrukcje dotyczące tworzenia klastra usługi HDInsight, zobacz [Rozpoczynanie](hadoop/apache-hadoop-linux-tutorial-get-started.md)pracy z Azure HDInsight .  

* W przypadku korzystania z programu PowerShell potrzebny będzie [moduł Az](/powershell/azure/). Upewnij się, że masz najnowszą wersję. W razie potrzeby uruchom `Update-Module -Name Az` .

* Jeśli chcesz korzystać z interfejsu wiersza polecenia platformy Azure i jeszcze go nie zainstalowano, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

> [!NOTE]  
> W celu lepszej wydajności zaleca się umieścić zarówno klaster usługi HDInsight, jak i obszar roboczy usługi Log Analytics w tym samym regionie. Azure Monitor nie są dostępne we wszystkich regionach świadczenia usługi Azure.

## <a name="enable-azure-monitor-using-the-portal"></a>Włączanie Azure Monitor przy użyciu portalu

W tej sekcji skonfigurujesz istniejący klaster usługi HDInsight Hadoop, aby używać obszaru roboczego usługi Azure Log Analytics do monitorowania zadań, debugowania dzienników i tak dalej.

1. Z [Azure Portal](https://portal.azure.com/)wybierz klaster. Klaster zostanie otwarty na nowej stronie portalu.

1. W obszarze Monitorowanie po **lewej stronie** wybierz pozycję **Azure Monitor**.

1. W widoku głównym w obszarze **integracja Azure Monitor** wybierz pozycję **Włącz.**

1. Z listy **rozwijanej Wybierz obszar** roboczy wybierz istniejący obszar roboczy usługi Log Analytics.

1. Wybierz pozycję **Zapisz**.  Zapisanie ustawienia może potrwać kilka minut.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="Włączanie monitorowania klastrów usługi HDInsight":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Włączanie Azure Monitor przy użyciu Azure PowerShell

Dzienniki dzienników Azure Monitor można włączyć przy użyciu Azure PowerShell Az [Enable-AzHDInsightMonitoring.](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring)

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Aby wyłączyć tę funkcję, użyj polecenia cmdlet [Disable-AzHDInsightMonitoring:](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Włączanie Azure Monitor przy użyciu interfejsu wiersza polecenia platformy Azure

Dzienniki dzienników Azure Monitor azure można włączyć przy użyciu polecenia interfejsu wiersza polecenia platformy Azure `[az hdinsight monitor enable` ](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable).

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Aby wyłączyć, użyj [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) polecenia .

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalowanie rozwiązań do zarządzania klastrami usługi HDInsight

Usługa HDInsight udostępnia rozwiązania do zarządzania specyficzne dla klastra, które można dodać na Azure Monitor klastra. [Rozwiązania do zarządzania](../azure-monitor/insights/solutions.md) dodają funkcjonalność do Azure Monitor, zapewniając dodatkowe narzędzia do analizy i danych. Te rozwiązania zbierają ważne metryki wydajności z klastrów usługi HDInsight. Udostępnij narzędzia do przeszukiwania metryk. Te rozwiązania zapewniają również wizualizacje i pulpity nawigacyjne dla większości typów klastrów obsługiwanych w umacie HDInsight. Korzystając z metryk zbieranych za pomocą rozwiązania, można tworzyć niestandardowe reguły monitorowania i alerty.

Dostępne rozwiązania HDInsight:

* HDInsight Hadoop Monitoring
* HDInsight HBase Monitoring
* Monitorowanie Interactive Query HDInsight
* Monitorowanie platformy Kafka w umacie HDInsight
* Monitorowanie platformy Spark w umacie HDInsight
* Monitorowanie systemu Storm w umacie HDInsight

Aby uzyskać instrukcje dotyczące rozwiązania do zarządzania, zobacz [Rozwiązania do zarządzania na platformie Azure.](../azure-monitor/insights/solutions.md#install-a-monitoring-solution) Aby poeksperymentować, zainstaluj rozwiązanie hdinsight hadoop monitoring. Gdy wszystko będzie gotowe, w obszarze Podsumowanie zostanie wyświetlony kafelek **HDInsightHadoop.** Wybierz **kafelek HDInsightHadoop.** Rozwiązanie HDInsightHadoop wygląda następująco:

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="Widok rozwiązania do monitorowania usługi HDInsight":::

Ponieważ klaster jest zupełnie nowym klastrem, raport nie pokazuje żadnych działań.

## <a name="configuring-performance-counters"></a>Konfigurowanie liczników wydajności

Usługa Azure Monitor obsługuje zbieranie i analizowanie metryk wydajności dla węzłów w klastrze. Aby uzyskać więcej informacji, zobacz [Źródła danych wydajności systemu Linux w Azure Monitor](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Inspekcja klastra

Usługa HDInsight obsługuje inspekcję klastra przy Azure Monitor, importując następujące typy dzienników:

* `log_gateway_audit_CL` — ta tabela zawiera dzienniki inspekcji z węzłów bramy klastra, które pokazują pomyślne i nieudane próby logowania.
* `log_auth_CL` — ta tabela zawiera dzienniki SSH z pomyślną i nieudaną próbą zalogowania.
* `log_ambari_audit_CL` — ta tabela zawiera dzienniki inspekcji z systemu Ambari.
* `log_ranger_audti_CL` — ta tabela zawiera dzienniki inspekcji z platformy Apache Ranger w klastrach ESP.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań Azure Monitor w celu monitorowania klastrów usługi HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Jak monitorować dostępność klastra za pomocą systemu Apache Ambari i Azure Monitor dzienników](./hdinsight-cluster-availability.md)