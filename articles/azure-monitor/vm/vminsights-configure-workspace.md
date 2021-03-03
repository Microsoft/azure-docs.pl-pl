---
title: Skonfiguruj obszar roboczy Log Analytics dla szczegółowych informacji o maszynie wirtualnej
description: Opisuje sposób tworzenia i konfigurowania obszaru roboczego Log Analytics używanego przez szczegółowe informacje o maszynie wirtualnej.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: dc7e6c42837ccaa56c7a211deb646c934ec137a4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717132"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>Skonfiguruj obszar roboczy Log Analytics dla szczegółowych informacji o maszynie wirtualnej
Usługi VM Insights zbiera dane z co najmniej jednego obszaru roboczego Log Analytics w Azure Monitor. Przed dołączeniem agentów należy utworzyć i skonfigurować obszar roboczy. W tym artykule opisano wymagania obszaru roboczego i skonfigurowano go na potrzeby usługi VM Insights.

## <a name="overview"></a>Omówienie
Pojedyncza subskrypcja może korzystać z dowolnej liczby obszarów roboczych w zależności od wymagań. Jedyne wymaganie obszaru roboczego polega na tym, że znajdują się one w obsługiwanej lokalizacji i są konfigurowane przy użyciu rozwiązania *VMInsights* .

Po skonfigurowaniu obszaru roboczego możesz użyć dowolnej z dostępnych opcji, aby zainstalować wymaganych agentów na maszynie wirtualnej i w zestawie skalowania maszyn wirtualnych, a następnie określić obszar roboczy, dla którego mają być wysyłane dane. Usługi VM Insights będą zbierać dane ze wszystkich skonfigurowanych obszarów roboczych w ramach subskrypcji.

> [!NOTE]
> Po włączeniu szczegółowego wglądu w dane maszyn wirtualnych w ramach jednej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych przy użyciu Azure Portal można wybrać istniejący obszar roboczy lub utworzyć nowy. Rozwiązanie *VMInsights* zostanie zainstalowane w tym obszarze roboczym, jeśli nie jest jeszcze. Następnie można użyć tego obszaru roboczego dla innych agentów.


## <a name="create-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

>[!NOTE]
>Informacje opisane w tej sekcji dotyczą również [rozwiązania Service map](service-map.md). 

Dostęp do Log Analytics obszarów roboczych w Azure Portal z menu **log Analytics obszary robocze** .

[![Obszary robocze Anlytics dziennika](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Nowy obszar roboczy Log Analytics można utworzyć przy użyciu dowolnej z poniższych metod. Zapoznaj się z artykułem [projektowanie wdrożenia dzienników Azure monitor](../logs/design-logs-deployment.md) , aby uzyskać wskazówki dotyczące określania liczby obszarów roboczych, które powinny być używane w danym środowisku, oraz sposobu projektowania strategii dostępu.


* [Witryna Azure Portal](../logs/quick-create-workspace.md)
* [Interfejs wiersza polecenia platformy Azure](../logs/quick-create-workspace-cli.md)
* [Program PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>Obsługiwane regiony
Usługi VM Insights obsługują obszar roboczy Log Analytics w każdym z [regionów obsługiwanych przez log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) z wyjątkiem następujących:

- Niemcy Środkowo-Zachodnie
- Korea Środkowa

>[!NOTE]
>Maszyny wirtualne platformy Azure można monitorować w dowolnym regionie. Same maszyny wirtualne nie są ograniczone do regionów obsługiwanych przez obszar roboczy Log Analytics.

## <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure
Aby włączyć funkcje w usłudze VM Insights i uzyskiwać do nich dostęp, musisz mieć [rolę współautor log Analytics](../logs/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym. Aby wyświetlić dane dotyczące wydajności, kondycji i mapy, musisz mieć [rolę czytnika monitorowania](../roles-permissions-security.md#built-in-monitoring-roles) dla maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego Log Analytics, zobacz [Zarządzanie obszarami roboczymi](../logs/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Dodaj rozwiązanie VMInsights do obszaru roboczego
Aby można było używać obszaru roboczego Log Analytics z usługą VM Insights, musi mieć zainstalowane rozwiązanie *VMInsights* . Metody konfigurowania obszaru roboczego są opisane w poniższych sekcjach.

> [!NOTE]
> Po dodaniu rozwiązania *VMInsights* do obszaru roboczego wszystkie istniejące maszyny wirtualne połączone z obszarem roboczym rozpoczną wysyłanie danych do InsightsMetrics. Dane dla innych typów danych nie będą zbierane do momentu dodania Agent zależności do tych istniejących maszyn wirtualnych połączonych z obszarem roboczym.

### <a name="azure-portal"></a>Azure Portal
Istnieją trzy opcje konfigurowania istniejącego obszaru roboczego przy użyciu Azure Portal. Każdy z nich został opisany poniżej.

Aby skonfigurować pojedynczy obszar roboczy, przejdź do opcji **Virtual Machines** w menu **Azure monitor** wybierz **inne opcje** dołączania, a następnie **Skonfiguruj obszar roboczy**. Wybierz subskrypcję i obszar roboczy, a następnie kliknij przycisk **Konfiguruj**.

[![Konfigurowanie obszaru roboczego](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Aby skonfigurować wiele obszarów roboczych, wybierz kartę **Konfiguracja obszaru roboczego** w menu **Virtual Machines** w menu **monitor** w Azure Portal. Ustaw wartości filtru, aby wyświetlić listę istniejących obszarów roboczych. Zaznacz pole wyboru obok każdego obszaru roboczego, aby włączyć, a następnie kliknij pozycję **Konfiguruj zaznaczone**.

[![Konfiguracja obszaru roboczego](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Po włączeniu szczegółowego wglądu w dane maszyn wirtualnych w ramach jednej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych przy użyciu Azure Portal można wybrać istniejący obszar roboczy lub utworzyć nowy. Rozwiązanie *VMInsights* zostanie zainstalowane w tym obszarze roboczym, jeśli nie jest jeszcze. Następnie można użyć tego obszaru roboczego dla innych agentów.

[![Włączanie pojedynczej maszyny wirtualnej w portalu](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Szablony Azure Resource Manager dla usługi VM Insights są dostępne w pliku archiwum (zip), który można [pobrać z naszego repozytorium GitHub](https://aka.ms/VmInsightsARMTemplates). Obejmuje to szablon o nazwie **ConfigureWorkspace** , który umożliwia skonfigurowanie obszaru roboczego log Analytics dla usługi VM Insights. Ten szablon jest wdrażany przy użyciu dowolnej z metod standardowych, w tym przykładowych poleceń PowerShell i interfejsu wiersza polecenia: 

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z tematem Dodawanie [agentów do usługi VM Insights](vminsights-enable-overview.md) , aby połączyć agentów ze szczegółowymi informacjami o maszynach wirtualnych.
- Zobacz [ukierunkowane rozwiązania do monitorowania w Azure monitor (wersja zapoznawcza)](../insights/solution-targeting.md) , aby ograniczyć ilość danych wysyłanych z rozwiązania do obszaru roboczego.
