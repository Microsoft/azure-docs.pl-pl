---
title: Rejestrowanie zasobów diagnostycznych dla sieciowej grupy zabezpieczeń
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak włączyć dzienniki zasobów diagnostycznych licznika zdarzeń i reguł dla sieciowej grupy zabezpieczeń platformy Azure.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: bb078b9738e995a1c507f7934a7dd64f075d5fe0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596531"
---
# <a name="resource-logging-for-a-network-security-group"></a>Rejestrowanie zasobów dla sieciowej grupy zabezpieczeń

Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) zawiera reguły zezwalające lub odmawiające ruchu do podsieci sieci wirtualnej, interfejsu sieciowego lub obu tych metod. 

Po włączeniu rejestrowania dla sieciowej grupy zabezpieczeń można zebrać następujące typy informacji dziennika zasobów:

* **Zdarzenie:** Wpisy są rejestrowane, dla których reguły sieciowej grupy zabezpieczeń są stosowane do maszyn wirtualnych na podstawie adresu MAC.
* **Licznik reguł:** Zawiera wpisy dotyczące liczby przypadków zastosowania każdej reguły sieciowej grupy zabezpieczeń do odmowy lub zezwolenia na ruch. Stan tych reguł jest zbierany co 300 sekund.

Dzienniki zasobów są dostępne tylko dla sieciowych grup zabezpieczeń wdrożonych za pomocą modelu wdrażania Azure Resource Manager. Nie można włączyć rejestrowania zasobów dla sieciowych grup zabezpieczeń wdrożonego za pomocą klasycznego modelu wdrażania. Aby lepiej zrozumieć te dwa modele, zobacz [Omówienie modeli wdrażania platformy Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Rejestrowanie zasobów jest włączane osobno dla *każdego* sieciowej grupy zabezpieczeńu, dla którego chcesz zbierać dane diagnostyczne. Jeśli chcesz zamiast tego zainteresować dzienniki aktywności (operacyjne), zobacz [Rejestrowanie aktywności](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)platformy Azure.

## <a name="enable-logging"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie zasobów, można użyć witryny [Azure Portal](#azure-portal), [programu PowerShell](#powershell)lub [interfejsu wiersza polecenia platformy Azure](#azure-cli) .

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [portalu](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi**, a następnie wpisz *Network Security Groups*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz je.
3. Wybierz sieciowej grupy zabezpieczeń, dla których chcesz włączyć rejestrowanie.
4. W obszarze **monitorowanie** wybierz pozycję **dzienniki diagnostyczne**, a następnie wybierz pozycję **Włącz diagnostykę**, jak pokazano na poniższej ilustracji:

   ![Włączanie diagnostyki](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. W obszarze **Ustawienia diagnostyki** wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **Zapisz**:

    | Ustawienie                                                                                     | Wartość                                                          |
    | ---------                                                                                   |---------                                                       |
    | Nazwa                                                                                        | Wybrana nazwa.  Na przykład: *myNsgDiagnostics*      |
    | **Archiwizuj na koncie magazynu**, **przesyła strumieniowo do centrum zdarzeń** i **wysyła do log Analytics** | Możesz wybrać dowolną liczbę miejsc docelowych. Aby dowiedzieć się więcej na temat każdego z nich, zobacz [miejsca docelowe dzienników](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Wybierz jedną lub obie kategorie dzienników. Aby dowiedzieć się więcej na temat danych zarejestrowanych dla każdej kategorii, zobacz [kategorie dzienników](#log-categories).                                                                                                                                             |
6. Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [przeglądanie i analizowanie dzienników](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić następujące polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. W przypadku uruchomienia programu PowerShell z komputera potrzebny jest moduł Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` polecenie na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie, `Connect-AzAccount` Aby zalogować się do platformy Azure przy użyciu konta, które ma [wymagane uprawnienia](virtual-network-network-interface.md#permissions).

Aby włączyć rejestrowanie zasobów, wymagany jest identyfikator istniejącej sieciowej grupy zabezpieczeń. Jeśli nie masz istniejącego sieciowej grupy zabezpieczeń, możesz go utworzyć za pomocą polecenie [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Pobierz sieciową grupę zabezpieczeń, dla której chcesz włączyć rejestrowanie zasobów w programie [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Aby na przykład pobrać sieciowej grupy zabezpieczeń o nazwie *myNsg* , który istnieje w grupie zasobów o nazwie Moja *zasobów*, wprowadź następujące polecenie:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Dzienniki zasobów można zapisać na trzech typach docelowych. Aby uzyskać więcej informacji, zobacz [miejsca docelowe dzienników](#log-destinations). W tym artykule dzienniki są wysyłane do lokalizacji docelowej *log Analytics* , na przykład. Pobierz istniejący obszar roboczy Log Analytics przy użyciu elementu [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Na przykład aby pobrać istniejący obszar roboczy o nazwie mój *obszar* roboczy w grupie zasobów o nazwie Moje *obszary robocze*, wprowadź następujące polecenie:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Jeśli nie masz istniejącego obszaru roboczego, możesz go utworzyć za pomocą polecenie [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Istnieją dwie kategorie rejestrowania, w których można włączyć dzienniki. Aby uzyskać więcej informacji, zobacz temat [kategorie dzienników](#log-categories). Włącz rejestrowanie zasobów dla sieciowej grupy zabezpieczeń za pomocą [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). Poniższy przykład rejestruje dane dotyczące zdarzenia i kategorii licznika w obszarze roboczym dla elementu sieciowej grupy zabezpieczeń, używając identyfikatorów dla sieciowej grupy zabezpieczeń i obszaru roboczego, który został wcześniej pobrany:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Jeśli chcesz tylko rejestrować dane dla jednej kategorii, a nie obu, Dodaj `-Categories` opcję do poprzedniego polecenia, a następnie *NetworkSecurityGroupEvent* lub *NetworkSecurityGroupRuleCounter*. Jeśli chcesz zarejestrować się w innym [miejscu docelowym](#log-destinations) niż obszar roboczy log Analytics, użyj odpowiednich parametrów dla konta usługi Azure [Storage](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) lub [centrum zdarzeń](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [przeglądanie i analizowanie dzienników](#view-and-analyze-logs).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Możesz uruchomić poniższe polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia platformy Azure na komputerze. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli interfejs wiersza polecenia zostanie uruchomiony z komputera, musisz mieć wersję 2.0.38 lub nowszą. Uruchom `az --version` polecenie na komputerze, aby znaleźć zainstalowaną wersję. Jeśli musisz przeprowadzić uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz też uruchomić polecenie, `az login` Aby zalogować się do platformy Azure przy użyciu konta, które ma [wymagane uprawnienia](virtual-network-network-interface.md#permissions).

Aby włączyć rejestrowanie zasobów, wymagany jest identyfikator istniejącej sieciowej grupy zabezpieczeń. Jeśli nie masz istniejącej sieciowej grupy zabezpieczeń, możesz ją utworzyć za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg#az-network-nsg-create).

Pobierz sieciową grupę zabezpieczeń, dla której chcesz włączyć rejestrowanie zasobów przy użyciu [AZ Network sieciowej grupy zabezpieczeń show](/cli/azure/network/nsg#az-network-nsg-show). Aby na przykład pobrać sieciowej grupy zabezpieczeń o nazwie *myNsg* , który istnieje w grupie zasobów o nazwie Moja *zasobów*, wprowadź następujące polecenie:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Dzienniki zasobów można zapisać na trzech typach docelowych. Aby uzyskać więcej informacji, zobacz [miejsca docelowe dzienników](#log-destinations). W tym artykule dzienniki są wysyłane do lokalizacji docelowej *log Analytics* , na przykład. Aby uzyskać więcej informacji, zobacz temat [kategorie dzienników](#log-categories).

Włącz rejestrowanie zasobów dla sieciowej grupy zabezpieczeń za pomocą [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). Poniższy przykład rejestruje dane kategorii zdarzenia i licznika w istniejącym obszarze roboczym o nazwie *webworkspace*, który istnieje w grupie zasobów o nazwie Moje *obszary robocze* i identyfikator sieciowej grupy zabezpieczeń, który został wcześniej pobrany:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Jeśli nie masz istniejącego obszaru roboczego, możesz go utworzyć przy użyciu [Azure Portal](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [programu PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Istnieją dwie kategorie rejestrowania, w których można włączyć dzienniki.

Jeśli chcesz tylko rejestrować dane dla jednej kategorii lub drugiej, Usuń kategorię, dla której chcesz rejestrować dane w poprzednim poleceniu. Jeśli chcesz zarejestrować się w innym [miejscu docelowym](#log-destinations) niż obszar roboczy log Analytics, użyj odpowiednich parametrów dla konta usługi Azure [Storage](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) lub [centrum zdarzeń](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [przeglądanie i analizowanie dzienników](#view-and-analyze-logs).

## <a name="log-destinations"></a>Miejsca docelowe dzienników

Dane diagnostyczne mogą być następujące:
- [Zapisywane na koncie usługi Azure Storage](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)w celu przeprowadzania inspekcji lub inspekcji ręcznej. Możesz określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych zasobu.
- [Przesyłane strumieniowo do centrum zdarzeń](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie do analizy, takie jak usługa PowerBI.
- [Zapisane w dziennikach Azure monitor](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).

## <a name="log-categories"></a>Kategorie dzienników

Dane w formacie JSON są zapisywane dla następujących kategorii dzienników:

### <a name="event"></a>Zdarzenie

Dziennik zdarzeń zawiera informacje o tym, które reguły sieciowej grupy zabezpieczeń są stosowane do maszyn wirtualnych na podstawie adresu MAC. Dla każdego zdarzenia rejestrowane są następujące dane. W poniższym przykładzie dane są rejestrowane dla maszyny wirtualnej z adresem IP 192.168.1.4 i adresem MAC 00-0D-3A-92-6A-7C:

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

Dziennik liczników reguł zawiera informacje dotyczące każdej reguły stosowanej do zasobów. Następujące przykładowe dane są rejestrowane za każdym razem, gdy reguła zostanie zastosowana. W poniższym przykładzie dane są rejestrowane dla maszyny wirtualnej z adresem IP 192.168.1.4 i adresem MAC 00-0D-3A-92-6A-7C:

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
> Źródłowy adres IP dla komunikacji nie jest zarejestrowany. Możesz włączyć [Rejestrowanie przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md) dla sieciowej grupy zabezpieczeń, które rejestruje wszystkie informacje o liczniku reguł, a także źródłowy adres IP, który zainicjował komunikację. Dane dziennika przepływu sieciowej grupy zabezpieczeń są zapisywane na koncie usługi Azure Storage. Dane można analizować za pomocą funkcji [analizy ruchu](../network-watcher/traffic-analytics.md) Network Watcher platformy Azure.

## <a name="view-and-analyze-logs"></a>Wyświetlanie i analizowanie dzienników

Aby dowiedzieć się, jak wyświetlać dane dziennika zasobów, zobacz [dzienniki platformy Azure — omówienie](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wysyłania danych diagnostycznych do:
- **Dzienniki Azure monitor**: Aby uzyskać szczegółowe informacje, możesz użyć rozwiązania do [analizy grup zabezpieczeń sieci](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) . Rozwiązanie udostępnia wizualizacje dla reguł sieciowej grupy zabezpieczeń, które zezwalają na ruch lub odmawiają go na adres MAC w maszynie wirtualnej.
- **Konto usługi Azure Storage**: dane są zapisywane w PT1H.jspliku. Można znaleźć następujące informacje:
  - Dziennik zdarzeń w następującej ścieżce: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Dziennik licznika reguł w następującej ścieżce: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [rejestrowaniu aktywności](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Rejestrowanie aktywności jest domyślnie włączone dla sieciowych grup zabezpieczeń utworzonych za pomocą modelu wdrażania platformy Azure. Aby określić, które operacje zostały wykonane w sieciowych grup zabezpieczeń w dzienniku aktywności, poszukaj wpisów zawierających następujące typy zasobów:
  - Microsoft. ClassicNetwork/networkSecurityGroups
  - Microsoft. ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft. Network/networkSecurityGroups
  - Microsoft. Network/networkSecurityGroups/securityRules
- Aby dowiedzieć się, jak rejestrować informacje diagnostyczne w celu uwzględnienia źródłowego adresu IP dla każdego przepływu, zobacz [Rejestrowanie przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
