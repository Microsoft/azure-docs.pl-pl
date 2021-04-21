---
title: Rejestrowanie zasobów diagnostycznych dla sieciowej grupy zabezpieczeń
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak włączyć dzienniki zasobów diagnostycznych liczników zdarzeń i reguł dla sieciowej grupy zabezpieczeń platformy Azure.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 0d171dee87a391c5e1d66db10363e6823ef387c1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774165"
---
# <a name="resource-logging-for-a-network-security-group"></a>Rejestrowanie zasobów dla sieciowej grupy zabezpieczeń

Sieciowa grupa zabezpieczeń (NSG) zawiera reguły, które zezwalają na ruch do podsieci sieci wirtualnej, interfejsu sieciowego lub obu tych reguł lub je odrzucają. 

Po włączeniu rejestrowania dla sieciowej grupy zabezpieczeń można zebrać następujące typy informacji dziennika zasobów:

* **Zdarzenie:** Rejestrowane są wpisy, dla których do maszyn wirtualnych są stosowane reguły sieciowej sieci na podstawie adresu MAC.
* **Licznik reguły:** Zawiera wpisy dotyczące tego, ile razy każda reguła sieciowej sieci jest stosowana w celu blokowania lub zezwalania na ruch. Stan tych reguł jest zbierany co 300 sekund.

Dzienniki zasobów są dostępne tylko dla sieciowych grup zabezpieczeń wdrożonych za pośrednictwem Azure Resource Manager wdrożenia. Nie można włączyć rejestrowania zasobów dla sieciowych grup zarządzania wdrożonych za pośrednictwem klasycznego modelu wdrażania. Aby lepiej zrozumieć te dwa modele, zobacz [Understanding Azure deployment models (Omówienie modeli wdrażania platformy Azure).](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Rejestrowanie zasobów jest włączane oddzielnie dla *każdej sieciowej* grupy danych, dla której chcesz zbierać dane diagnostyczne. Jeśli zamiast tego interesują Cię dzienniki aktywności (operacyjne), zobacz Rejestrowanie aktywności [platformy](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Azure. Jeśli interesuje Cię ruch IP przepływający przez sieciowe sieciowe gs, zobacz dzienniki przepływu sieciowej Network Watcher Azure Network Watcher [sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-overview.md) 

## <a name="enable-logging"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie zasobów, możesz użyć witryny [Azure Portal,](#azure-portal) [programu PowerShell](#powershell)lub interfejsu wiersza polecenia platformy [Azure.](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [portalu](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi,** a następnie wpisz *sieciowe grupy zabezpieczeń.* Gdy **sieciowe grupy zabezpieczeń pojawią** się w wynikach wyszukiwania, wybierz je.
3. Wybierz sieciowa nr 5, dla której chcesz włączyć rejestrowanie.
4. W **obszarze MONITOROWANIE** wybierz pozycję Dzienniki **diagnostyczne,** a następnie wybierz pozycję **Włącz diagnostykę**, jak pokazano na poniższej ilustracji:

   ![Włączanie diagnostyki](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. W **obszarze Ustawienia diagnostyki** wprowadź lub wybierz następujące informacje, a następnie wybierz pozycję **Zapisz:**

    | Ustawienie                                                                                     | Wartość                                                          |
    | ---------                                                                                   |---------                                                       |
    | Nazwa                                                                                        | Nazwa, która jest wybierana.  Na przykład: *myNsgDiagnostics*      |
    | **Archiwizowanie na koncie magazynu,** **przesyłanie strumieniowe do centrum zdarzeń** i wysyłanie do usługi Log **Analytics** | Możesz wybrać tyle miejsc docelowych, ile wybierzesz. Aby dowiedzieć się więcej na temat każdego z nich, zobacz [Log destinations (Miejsca docelowe dzienników).](#log-destinations)                                                                                                                                           |
    | LOG                                                                                         | Wybierz jedną lub obie kategorie dzienników. Aby dowiedzieć się więcej o danych rejestrowanych dla każdej kategorii, zobacz [Kategorie dzienników](#log-categories).                                                                                                                                             |
6. Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i analizowanie dzienników.](#view-and-analyze-logs)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić polecenia, które należy wykonać w [Azure Cloud Shell](https://shell.azure.com/powershell), lub uruchamiając program PowerShell z komputera. Ta Azure Cloud Shell jest bezpłatną interaktywną powłoką. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli uruchamiasz program PowerShell z komputera, potrzebujesz modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz również uruchomić narzędzie , aby zalogować się do platformy Azure przy użyciu konta z `Connect-AzAccount` [niezbędnymi uprawnieniami.](virtual-network-network-interface.md#permissions)

Aby włączyć rejestrowanie zasobów, potrzebny jest identyfikator istniejącej sieciowej grupy zasobów. Jeśli nie masz istniejącej sieciowej grupy sieciowej, możesz ją utworzyć za pomocą [nowego pliku New-AzNetworkSecurityGroup.](/powershell/module/az.network/new-aznetworksecuritygroup)

Pobierz sieciową grupę zabezpieczeń, dla której chcesz włączyć rejestrowanie zasobów, za pomocą [get-AzNetworkSecurityGroup.](/powershell/module/az.network/get-aznetworksecuritygroup) Aby na przykład pobrać sieciową grupę danych o nazwie *myNsg,* która istnieje w grupie zasobów o nazwie *myResourceGroup,* wprowadź następujące polecenie:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Dzienniki zasobów można zapisywać w trzech typach docelowych. Aby uzyskać więcej informacji, zobacz [Miejsca docelowe dzienników](#log-destinations). W tym artykule dzienniki są na przykład wysyłane do miejsca docelowego usługi *Log Analytics.* Pobierz istniejący obszar roboczy usługi Log Analytics za pomocą [get-AzOperationalInsightsWorkspace.](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) Aby na przykład pobrać istniejący obszar roboczy o nazwie *myWorkspace* w grupie zasobów o nazwie *myWorkspaces,* wprowadź następujące polecenie:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Jeśli nie masz istniejącego obszaru roboczego, możesz go utworzyć za pomocą [new-AzOperationalInsightsWorkspace.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace)

Istnieją dwie kategorie rejestrowania, dla których można włączyć dzienniki. Aby uzyskać więcej informacji, zobacz [Kategorie dzienników](#log-categories). Włącz rejestrowanie zasobów dla sieciowej grupy zasobów za pomocą [set-AzDiagnosticSetting.](/powershell/module/az.monitor/set-azdiagnosticsetting) Poniższy przykład rejestruje dane kategorii zdarzeń i liczników w obszarze roboczym sieciowej grupy zabezpieczeń przy użyciu identyfikatorów sieciowej grupy zabezpieczeń i obszaru roboczego pobranych wcześniej:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Jeśli chcesz rejestrować dane tylko dla jednej lub drugiej kategorii, a nie obu, dodaj opcję do poprzedniego polecenia, a następnie pozycję `-Categories` *NetworkSecurityGroupEvent* lub *NetworkSecurityGroupRuleCounter.* Jeśli chcesz zalogować się [](#log-destinations) do innego miejsca docelowego niż obszar roboczy usługi Log Analytics, użyj odpowiednich parametrów dla konta usługi Azure [Storage](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) lub [centrum zdarzeń.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)

Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i analizowanie dzienników.](#view-and-analyze-logs)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Możesz uruchomić polecenia, które są następujące w [witrynie Azure Cloud Shell](https://shell.azure.com/bash), lub uruchamiając interfejs wiersza polecenia platformy Azure z komputera. Interfejs Azure Cloud Shell jest bezpłatną interaktywną powłoką. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli uruchamiasz interfejs wiersza polecenia z komputera, potrzebujesz wersji 2.0.38 lub nowszej. Uruchom `az --version` program na komputerze, aby znaleźć zainstalowaną wersję. Jeśli musisz uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz również uruchomić narzędzie , aby zalogować się na platformie Azure przy użyciu konta z `az login` [niezbędnymi uprawnieniami.](virtual-network-network-interface.md#permissions)

Aby włączyć rejestrowanie zasobów, potrzebny jest identyfikator istniejącej sieciowej grupy zasobów. Jeśli nie masz istniejącej sieciowej grupy sieciowej, możesz ją utworzyć za pomocą az [network nsg create](/cli/azure/network/nsg#az_network_nsg_create).

Pobierz sieciową grupę zabezpieczeń, dla której chcesz włączyć rejestrowanie zasobów, za pomocą [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show). Aby na przykład pobrać sieciową grupę danych o nazwie *myNsg,* która istnieje w grupie zasobów o nazwie *myResourceGroup,* wprowadź następujące polecenie:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Dzienniki zasobów można zapisywać w trzech typach docelowych. Aby uzyskać więcej informacji, zobacz [Log destinations (Miejsca docelowe dzienników).](#log-destinations) Na przykład w tym artykule dzienniki są wysyłane do miejsca docelowego usługi *Log Analytics.* Aby uzyskać więcej informacji, zobacz [Kategorie dzienników](#log-categories).

Włącz rejestrowanie zasobów dla sieciowej grupy zabezpieczeń za pomocą [narzędzia az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) Poniższy przykład rejestruje dane kategorii zdarzeń i liczników w istniejącym obszarze roboczym o nazwie *myWorkspace,* który istnieje w grupie zasobów o nazwie *myWorkspaces,* oraz identyfikator wcześniej pobranej sieciowej grupy zabezpieczeń:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Jeśli nie masz istniejącego obszaru roboczego, możesz go utworzyć przy użyciu narzędzia [Azure Portal](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [programu PowerShell.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) Istnieją dwie kategorie rejestrowania, dla których można włączyć dzienniki.

Jeśli chcesz rejestrować dane tylko dla jednej kategorii lub innej, usuń kategorię, dla której nie chcesz rejestrować danych w poprzednim poleceniu. Jeśli chcesz zalogować się do innego miejsca [docelowego](#log-destinations) niż obszar roboczy usługi Log Analytics, użyj odpowiednich parametrów dla konta usługi Azure [Storage](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) lub [centrum zdarzeń.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)

Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i analizowanie dzienników.](#view-and-analyze-logs)

## <a name="log-destinations"></a>Miejsca docelowe dzienników

Dane diagnostyczne mogą być:
- [Zapisane na koncie usługi Azure Storage](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)w celu inspekcji lub ręcznej inspekcji. Czas przechowywania (w dniach) można określić przy użyciu ustawień diagnostycznych zasobów.
- [Przesyłane strumieniowo do centrum zdarzeń](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne, takie jak usługa PowerBI.
- [Zapisywany w Azure Monitor dziennikach.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)

## <a name="log-categories"></a>Kategorie dzienników

Dane w formacie JSON są zapisywane dla następujących kategorii dzienników:

### <a name="event"></a>Zdarzenie

Dziennik zdarzeń zawiera informacje o tym, które reguły sieciowej organizacji zabezpieczeń są stosowane do maszyn wirtualnych, na podstawie adresu MAC. Następujące dane są rejestrowane dla każdego zdarzenia. W poniższym przykładzie dane są rejestrowane dla maszyny wirtualnej o adresie IP 192.168.1.4 i adresie MAC 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Licznik reguł

Dziennik liczników reguł zawiera informacje o każdej regułie zastosowanej do zasobów. Poniższe przykładowe dane są rejestrowane za każdym razem, gdy reguła jest stosowana. W poniższym przykładzie dane są rejestrowane dla maszyny wirtualnej o adresie IP 192.168.1.4 i adresie MAC 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Źródłowy adres IP komunikacji nie jest rejestrowany. Można jednak [włączyć](../network-watcher/network-watcher-nsg-flow-logging-portal.md) rejestrowanie przepływu sieciowej organizacji zabezpieczeń dla sieciowej sieci, która rejestruje wszystkie informacje licznika reguł, a także źródłowy adres IP, który zainicjował komunikację. Dane dziennika przepływu sieciowej grupy zabezpieczeń są zapisywane na koncie usługi Azure Storage. Dane można analizować za pomocą funkcji [analizy ruchu](../network-watcher/traffic-analytics.md) w usłudze Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Wyświetlanie i analizowanie dzienników

Aby dowiedzieć się, jak wyświetlać dane dziennika zasobów, zobacz [Omówienie dzienników platformy Azure.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) W przypadku wysyłania danych diagnostycznych do:
- **Azure Monitor dziennikach:** możesz użyć rozwiązania do analizy [sieciowych grup](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) zabezpieczeń w celu zwiększenia szczegółowych informacji. Rozwiązanie udostępnia wizualizacje dla reguł sieciowej organizacji sieciowej, które zezwalają na ruch lub go odrzucają na adres MAC interfejsu sieciowego w maszynie wirtualnej.
- **Konto usługi Azure Storage:** dane są zapisywane w PT1H.jsw pliku. Możesz znaleźć:
  - Dziennik zdarzeń w następującej ścieżce: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Dziennik licznika reguły w następującej ścieżce: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [o rejestrowaniu aktywności.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rejestrowanie aktywności jest domyślnie włączone dla sieciowych grupy zarządzania utworzonych za pomocą jednego z modeli wdrażania platformy Azure. Aby określić, które operacje zostały wykonane w sieciowych grupach zabezpieczeń w dzienniku aktywności, poszukaj wpisów zawierających następujące typy zasobów:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Aby dowiedzieć się, jak rejestrować informacje diagnostyczne, aby uwzględnić źródłowy adres IP dla każdego przepływu, zobacz Rejestrowanie przepływu [sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
