---
title: Monitorowanie wydajności za pomocą dzienników Azure Monitor
description: Dowiedz się, jak skonfigurować agenta Log Analytics na potrzeby monitorowania kontenerów i liczników wydajności dla klastrów Service Fabric platformy Azure.
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 9bb89dc2eebe584a0a9f81a6707c0a2e4fa2fc30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626677"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitorowanie wydajności za pomocą dzienników Azure Monitor

W tym artykule opisano procedurę dodawania agenta Log Analytics jako rozszerzenia zestawu skalowania maszyn wirtualnych do klastra i łączenia go z istniejącym obszarem roboczym usługi Azure Log Analytics. Umożliwia to zbieranie danych diagnostycznych dotyczących kontenerów, aplikacji i monitorowania wydajności. Przez dodanie go jako rozszerzenia do zasobu zestawu skalowania maszyn wirtualnych, Azure Resource Manager zapewnia, że zostanie on zainstalowany w każdym węźle, nawet podczas skalowania klastra.

> [!NOTE]
> W tym artykule przyjęto założenie, że masz już skonfigurowany obszar roboczy usługi Azure Log Analytics. Jeśli nie, przejdź do sekcji [Konfigurowanie dzienników Azure monitor](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Dodawanie rozszerzenia agenta za pomocą interfejsu wiersza polecenia platformy Azure

Najlepszym sposobem dodawania agenta Log Analytics do klastra jest za pośrednictwem interfejsów API zestawu skalowania maszyn wirtualnych dostępnych w interfejsie wiersza polecenia platformy Azure. Jeśli nie masz jeszcze skonfigurowanego interfejsu wiersza polecenia platformy Azure, przejdź do Azure Portal i Otwórz wystąpienie [Cloud Shell](../cloud-shell/overview.md) lub [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Po zażądaniu Cloud Shell upewnij się, że Pracujesz w tej samej subskrypcji co zasób. Sprawdź to `az account show` i upewnij się, że wartość "name" jest zgodna z subskrypcją klastra.

2. W portalu przejdź do grupy zasobów, w której znajduje się obszar roboczy Log Analytics. Kliknij zasób usługi log Analytics (typ zasobu zostanie Log Analytics obszarze roboczym). Gdy jesteś na stronie Przegląd zasobów, kliknij pozycję **Ustawienia zaawansowane** w sekcji Ustawienia w menu po lewej stronie.

    ![Strona właściwości usługi log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Kliknij pozycję **serwery z systemem Windows** , jeśli tworzysz klaster systemu Windows i serwery z systemem **Linux** w przypadku tworzenia klastra z systemem Linux. Na tej stronie będzie widoczny Twój `workspace ID` i `workspace key` (wymieniony jako klucz podstawowy w portalu). W następnym kroku będą potrzebne oba elementy.

4. Uruchom polecenie, aby zainstalować agenta Log Analytics w klastrze przy użyciu `vmss extension set` interfejsu API:

    W przypadku klastra systemu Windows:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    W przypadku klastra z systemem Linux:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Oto przykład agenta Log Analytics dodawanego do klastra systemu Windows.

    ![Polecenie interfejsu wiersza polecenia Log Analytics Agent](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. To potrwa mniej niż 15 minut, aby pomyślnie dodać agenta do węzłów. Można sprawdzić, czy agenci zostali dodani za pomocą `az vmss extension list` interfejsu API:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Dodaj agenta za pomocą szablonu Menedżer zasobów

Przykładowe szablony Menedżer zasobów, które wdrażają obszar roboczy platformy Azure Log Analytics i dodają agenta do każdego z węzłów, są dostępne dla [systemu Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) lub [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

Możesz pobrać i zmodyfikować ten szablon, aby wdrożyć klaster, który najlepiej odpowiada Twoim potrzebom.

## <a name="view-performance-counters"></a>Wyświetlanie liczników wydajności

Teraz, po dodaniu agenta Log Analytics, przejdź do portalu Log Analytics, aby wybrać liczniki wydajności, które chcesz zebrać.

1. W Azure Portal przejdź do grupy zasobów, w której utworzono rozwiązanie Service Fabric Analytics. Wybierz pozycję **servicefabric \<nameOfLog AnalyticsWorkspace\>**.

2. Kliknij pozycję **Log Analytics**.

3. Kliknij pozycję **Ustawienia zaawansowane**.

4. Kliknij pozycję **dane**, a następnie kliknij pozycję **liczniki wydajności systemu Windows lub Linux**. Istnieje lista domyślnych liczników, które można włączyć, i można ustawić interwał dla kolekcji. Możesz również dodać [Dodatkowe liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md) do zbierania. W tym [artykule](/windows/win32/perfctrs/specifying-a-counter-path)jest przywoływany właściwy format.

5. Kliknij przycisk **Zapisz**, a następnie kliknij przycisk **OK**.

6. Zamknij blok ustawienia zaawansowane.

7. W obszarze Ogólne kliknij pozycję **Podsumowanie obszaru roboczego**.

8. Zostaną wyświetlone kafelki w formie wykresu dla każdej z włączonych rozwiązań, w tym jeden dla Service Fabric. Kliknij wykres **Service Fabric** , aby kontynuować Service Fabric Analytics rozwiązanie.

9. Zobaczysz kilka kafelków z wykresami dotyczącymi zdarzeń kanałów operacyjnych i niezawodnych usług. Graficzna reprezentacja danych przepływających dla wybranych liczników będzie wyświetlana w obszarze metryki węzła.

10. Kliknij wykres metryki kontenera, aby wyświetlić dodatkowe szczegóły. Można również wykonywać zapytania dotyczące danych licznika wydajności podobnie jak zdarzenia klastra i filtrować w węzłach, nazwach liczników wydajności i wartościach przy użyciu języka zapytań Kusto.

![Zapytanie o licznik wydajności Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Następne kroki

* Zbierz odpowiednie [liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md). Aby skonfigurować agenta Log Analytics do zbierania określonych liczników wydajności, zapoznaj się z tematem [Konfigurowanie źródeł danych](../azure-monitor/agents/agent-data-sources.md#configuring-data-sources).
* Konfigurowanie dzienników Azure Monitor, aby skonfigurować [Automatyczne alerty](../azure-monitor/alerts/alerts-overview.md) w celu ułatwienia wykrywania i diagnostyki
* Alternatywnie można zbierać liczniki wydajności przy użyciu [rozszerzenia Diagnostyka Azure i wysyłać je do Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
