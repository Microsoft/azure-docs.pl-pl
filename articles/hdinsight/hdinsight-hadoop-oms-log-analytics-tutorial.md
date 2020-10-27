---
title: Używanie dzienników Azure Monitor do monitorowania klastrów usługi Azure HDInsight
description: Informacje dotyczące monitorowania zadań uruchomionych w klastrze usługi HDInsight przy użyciu dzienników Azure Monitor.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 775e2fad573832dd29fc45985c6d6bd0a50fdf3c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546096"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Monitorowanie klastrów usługi HDInsight za pomocą dzienników usługi Azure Monitor

Dowiedz się, jak włączyć dzienniki Azure Monitor, aby monitorować operacje klastra Hadoop w usłudze HDInsight. I jak dodać rozwiązanie do monitorowania usługi HDInsight.

[Dzienniki Azure monitor](../azure-monitor/log-query/log-query-overview.md) to usługa Azure monitor, która monitoruje środowiska chmurowe i lokalne. Monitorowanie ma na celu zachowanie ich dostępności i wydajności. Zbiera dane generowane przez zasoby w chmurze, w środowiskach lokalnych i innych narzędziach do monitorowania. Dane służą do zapewnienia analiz w wielu źródłach.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Log Analytics. Ten obszar roboczy można traktować jako unikatowe środowisko dzienników Azure Monitor z własnym repozytorium danych, źródłami danych i rozwiązaniami. Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Klaster usługi Azure HDInsight. Obecnie można użyć dzienników Azure Monitor z następującymi typami klastrów usługi HDInsight:

  * Hadoop
  * HBase
  * Zapytanie interakcyjne
  * Kafka
  * platforma Spark
  * Storm

  Instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight można znaleźć w temacie [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* W przypadku korzystania z programu PowerShell należy użyć polecenia [AZ module](/powershell/azure/). Upewnij się, że masz najnowszą wersję. W razie potrzeby uruchom polecenie `Update-Module -Name Az` .

* Jeśli chcesz użyć interfejsu wiersza polecenia platformy Azure i jeszcze go nie zainstalowano, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

> [!NOTE]  
> Zaleca się umieszczenie klastra usługi HDInsight i obszaru roboczego Log Analytics w tym samym regionie w celu uzyskania lepszej wydajności. Dzienniki Azure Monitor nie są dostępne we wszystkich regionach świadczenia usługi Azure.

## <a name="enable-azure-monitor-using-the-portal"></a>Włączanie Azure Monitor przy użyciu portalu

W tej sekcji skonfigurujesz istniejący klaster usługi HDInsight Hadoop do używania obszaru roboczego Log Analytics platformy Azure do monitorowania zadań, debugowania dzienników itd.

1. Na [Azure Portal](https://portal.azure.com/)wybierz swój klaster. Klaster zostanie otwarty na nowej stronie portalu.

1. W obszarze **monitorowanie** wybierz pozycję **Azure monitor** .

1. W widoku głównym w obszarze **integracja Azure monitor** wybierz pozycję **Włącz** .

1. Z listy rozwijanej **Wybierz obszar roboczy** wybierz istniejący obszar roboczy log Analytics.

1. Wybierz pozycję **Zapisz** .  Zapisanie tego ustawienia trwa kilka chwil.

    ![Włącz monitorowanie klastrów usługi HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Włącz monitorowanie klastrów usługi HDInsight")

## <a name="enable-azure-monitor-using-azure-powershell"></a>Włącz Azure Monitor przy użyciu Azure PowerShell

Dzienniki Azure Monitor można włączyć za Azure PowerShell pomocą polecenia cmdlet AZ module [enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) .

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

Aby wyłączyć, użyj polecenia cmdlet [disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) :

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Włączanie Azure Monitor przy użyciu interfejsu wiersza polecenia platformy Azure

Dzienniki Azure Monitor można włączyć przy użyciu interfejsu wiersza polecenia platformy Azure `[az hdinsight monitor enable` ] (/CLI/Azure/HDInsight/monitor # AZ-HDInsight-monitor-Enable).

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

Aby wyłączyć, użyj [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az-hdinsight-monitor-disable) polecenia.

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalowanie rozwiązań do zarządzania klastrami usługi HDInsight

Usługa HDInsight udostępnia rozwiązania do zarządzania specyficzne dla klastra, które można dodać do dzienników Azure Monitor. [Rozwiązania do zarządzania](../azure-monitor/insights/solutions.md) umożliwiają dodawanie funkcji do dzienników Azure monitor, które udostępniają dodatkowe narzędzia do obsługi danych i analizy. Te rozwiązania zbierają ważne metryki wydajności z klastrów usługi HDInsight. I podaj narzędzia do wyszukiwania metryk. Te rozwiązania udostępniają również wizualizacje i pulpity nawigacyjne dla większości typów klastrów obsługiwanych w usłudze HDInsight. Korzystając z metryk zbieranych z rozwiązaniem, można tworzyć niestandardowe reguły monitorowania i alerty.

Dostępne rozwiązania usługi HDInsight:

* Monitorowanie usługi HDInsight Hadoop
* HDInsight HBase Monitoring
* Monitorowanie interakcyjnych zapytań usługi HDInsight
* Monitorowanie usługi HDInsight Kafka
* Monitorowanie usługi HDInsight Spark
* Monitorowanie burzy usługi HDInsight

Aby uzyskać instrukcje dotyczące rozwiązania do zarządzania, zobacz [rozwiązania do zarządzania na platformie Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Aby eksperymentować, Zainstaluj rozwiązanie do monitorowania usługi HDInsight Hadoop. Gdy wszystko będzie gotowe, zobaczysz kafelek **HDInsightHadoop** na liście **Podsumowanie** . Wybierz kafelek **HDInsightHadoop** . Rozwiązanie HDInsightHadoop wygląda następująco:

![Widok rozwiązania do monitorowania usługi HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Ponieważ klaster jest zupełnie nowym klastrem, raport nie zawiera żadnych działań.

## <a name="configuring-performance-counters"></a>Konfigurowanie liczników wydajności

Usługa Azure monitor obsługuje zbieranie i analizowanie metryk wydajności dla węzłów w klastrze. Aby uzyskać więcej informacji, zobacz [źródła danych wydajności systemu Linux w Azure monitor](../azure-monitor/platform/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Inspekcja klastra

Usługa HDInsight obsługuje inspekcję klastra przy użyciu dzienników Azure Monitor przez zaimportowanie następujących typów dzienników:

* `log_gateway_audit_CL` -Ta tabela zawiera dzienniki inspekcji z węzłów bramy klastra, które pokazują pomyślne i nieudane próby zalogowania.
* `log_auth_CL` -Ta tabela zawiera dzienniki protokołu SSH z pomyślnymi i nieudanymi próbami logowania.
* `log_ambari_audit_CL` -Ta tabela zawiera dzienniki inspekcji z Ambari.
* `log_ranger_audti_CL` -Ta tabela zawiera dzienniki inspekcji z platformy Apache Ranger na klastrach ESP.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących dzienników Azure Monitor do monitorowania klastrów usługi HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Jak monitorować dostępność klastra za pomocą programu Apache Ambari i dzienników Azure Monitor](./hdinsight-cluster-availability.md)