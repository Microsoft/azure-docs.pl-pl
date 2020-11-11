---
title: Skonfiguruj obszar roboczy Log Analytics dla Azure Monitor dla maszyn wirtualnych
description: Opisuje sposób tworzenia i konfigurowania obszaru roboczego Log Analytics używanego przez Azure Monitor dla maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 9827f79f8407bd04468f1b4456aec9d6a8793a6f
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506298"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Skonfiguruj obszar roboczy Log Analytics dla Azure Monitor dla maszyn wirtualnych
Azure Monitor dla maszyn wirtualnych zbiera dane z co najmniej jednego Log Analytics obszarów roboczych w Azure Monitor. Przed dołączeniem agentów należy utworzyć i skonfigurować obszar roboczy. W tym artykule opisano wymagania obszaru roboczego i skonfigurować go do Azure Monitor dla maszyn wirtualnych.

## <a name="overview"></a>Omówienie
Pojedyncza subskrypcja może korzystać z dowolnej liczby obszarów roboczych, w zależności od wymagań, tylko wymóg tego obszaru roboczego jest zlokalizowany w obsługiwanej lokalizacji i skonfigurowany przy użyciu rozwiązania *VMInsights* .

Po skonfigurowaniu obszaru roboczego możesz użyć dowolnej z dostępnych opcji, aby zainstalować wymaganych agentów na maszynie wirtualnej i VMSS, a następnie określić obszar roboczy, który wyśle ich dane. Azure Monitor dla maszyn wirtualnych będzie zbierać dane ze wszystkich skonfigurowanych obszarów roboczych w ramach subskrypcji.

> [!NOTE]
> Po włączeniu Azure Monitor dla maszyn wirtualnych na jednej maszynie wirtualnej lub VMSS za pomocą Azure Portal można wybrać istniejący obszar roboczy lub utworzyć nowy. Rozwiązanie *VMInsights* zostanie zainstalowane w tym obszarze roboczym, jeśli nie jest jeszcze. Następnie można użyć tego obszaru roboczego dla innych agentów.


## <a name="create-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

>[!NOTE]
>Informacje opisane w tej sekcji dotyczą również [rozwiązania Service map](service-map.md). 

Dostęp do Log Analytics obszarów roboczych w Azure Portal z menu **log Analytics obszary robocze** .

[![Obszary robocze Anlytics dziennika](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Nowy obszar roboczy Log Analytics można utworzyć przy użyciu dowolnej z poniższych metod. Zapoznaj się z artykułem [projektowanie wdrożenia dzienników Azure monitor](../platform/design-logs-deployment.md) , aby uzyskać wskazówki dotyczące określania liczby obszarów roboczych, które powinny być używane w danym środowisku, oraz sposobu projektowania strategii dostępu.


* [Witryna Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Interfejs wiersza polecenia platformy Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [Program PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>Obsługiwane regiony

Azure Monitor dla maszyn wirtualnych obsługuje Log Analytics obszarów roboczych w następujących regionach, chociaż można monitorować maszyny wirtualne w dowolnym regionie. Same maszyny wirtualne nie są ograniczone do regionów obsługiwanych przez obszar roboczy Log Analytics.

- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- South Central US
- East US
- Wschodnie stany USA 2
- Central US
- Północno-środkowe stany USA
- US Gov AZ
- US Gov VA
- Kanada Środkowa
- Południowe Zjednoczone Królestwo
- Europa Północna
- West Europe
- Azja Wschodnia
- Southeast Asia
- Indie Środkowe
- Japan East
- Australia Wschodnia
- Australia Południowo-Wschodnia

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Aby włączyć funkcje i uzyskać do nich dostęp w Azure Monitor dla maszyn wirtualnych, musisz mieć [rolę współautor log Analytics](../platform/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym. Aby wyświetlić dane dotyczące wydajności, kondycji i mapy, musisz mieć [rolę czytnika monitorowania](../platform/roles-permissions-security.md#built-in-monitoring-roles) dla maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego Log Analytics, zobacz [Zarządzanie obszarami roboczymi](../platform/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Dodaj rozwiązanie VMInsights do obszaru roboczego
Aby można było używać Log Analyticsego obszaru roboczego z Azure Monitor dla maszyn wirtualnych, musi mieć zainstalowane rozwiązanie *VMInsights* . Metody konfigurowania obszaru roboczego są opisane w poniższych sekcjach.

> [!NOTE]
> Po dodaniu rozwiązania *VMInsights* do obszaru roboczego wszystkie istniejące maszyny wirtualne połączone z obszarem roboczym rozpoczną wysyłanie danych do InsightsMetrics. Dane dla innych typów danych nie będą zbierane do momentu dodania Agent zależności do tych istniejących maszyn wirtualnych połączonych z obszarem roboczym.

### <a name="azure-portal"></a>Witryna Azure Portal
Istnieją trzy opcje konfigurowania istniejącego obszaru roboczego na podstawie Azure Portal.

Aby skonfigurować jeden obszar roboczy, wybierz **inne opcje** dołączania, a następnie **Skonfiguruj obszar roboczy**. Wybierz subskrypcję i obszar roboczy, a następnie kliknij przycisk **Konfiguruj**.

[![Konfigurowanie obszaru roboczego](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

Aby skonfigurować wiele obszarów roboczych, wybierz kartę **Konfiguracja obszaru roboczego** w menu **Virtual Machines** w menu **monitor** w Azure Portal. Ustaw wartości filtru, aby wyświetlić listę istniejących obszarów roboczych. Zaznacz pole wyboru obok każdego obszaru roboczego, aby włączyć, a następnie kliknij pozycję **Konfiguruj zaznaczone** .

[![Konfiguracja obszaru roboczego](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


Po włączeniu Azure Monitor dla maszyn wirtualnych na jednej maszynie wirtualnej lub VMSS za pomocą Azure Portal można wybrać istniejący obszar roboczy lub utworzyć nowy. Rozwiązanie *VMInsights* zostanie zainstalowane w tym obszarze roboczym, jeśli nie jest jeszcze. Następnie można użyć tego obszaru roboczego dla innych agentów.

[![Włączanie pojedynczej maszyny wirtualnej w portalu](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Szablony Azure Resource Manager dla Azure Monitor dla maszyn wirtualnych są dostępne w pliku archiwum (zip), który można [pobrać z naszego repozytorium GitHub](https://aka.ms/VmInsightsARMTemplates). Obejmuje to szablon o nazwie **ConfigureWorkspace** , który konfiguruje obszar roboczy Log Analytics dla Azure monitor dla maszyn wirtualnych. Ten szablon jest wdrażany przy użyciu dowolnej z metod standardowych, w tym przykładowych poleceń PowerShell i interfejsu wiersza polecenia: 

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
- Zapoznaj się z tematem Dodawanie [agentów do Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md) , aby połączyć agentów z Azure monitor dla maszyn wirtualnych.
- Zobacz [ukierunkowane rozwiązania do monitorowania w Azure monitor (wersja zapoznawcza)](solution-targeting.md) , aby ograniczyć ilość danych wysyłanych z rozwiązania do obszaru roboczego.
