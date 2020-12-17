---
title: Network Watcher — Tworzenie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu szablonu Azure Resource Manager
description: Użyj szablonu Azure Resource Manager i programu PowerShell, aby łatwo skonfigurować dzienniki przepływu sieciowej grupy zabezpieczeń.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: c3ac2eab68963d75cd5c916e06285d49edd37aed
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656091"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń na podstawie szablonu Azure Resource Manager

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) to natywny i zaawansowany sposób zarządzania [infrastrukturą jako kod](/azure/devops/learn/what-is-infrastructure-as-code).

W tym artykule przedstawiono sposób programowego włączania [dzienników usługi sieciowej grupy zabezpieczeń Flow](./network-watcher-nsg-flow-logging-overview.md) przy użyciu szablonu Azure Resource Manager i Azure PowerShell. Zaczynamy od udostępnienia przeglądu właściwości obiektu dziennika przepływu sieciowej grupy zabezpieczeń, po którym następuje kilka przykładowych szablonów. Następnie wdrażamy szablon przy użyciu lokalnego wystąpienia programu PowerShell.


## <a name="nsg-flow-logs-object"></a>Obiekt dzienników przepływu sieciowej grupy zabezpieczeń

Obiekt dzienników przepływu sieciowej grupy zabezpieczeń ze wszystkimi parametrami jest przedstawiony poniżej.
Pełny przegląd właściwości można znaleźć w [dokumentacji szablonów przepływu sieciowej grupy zabezpieczeń](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Aby utworzyć zasób Microsoft. Network/networkWatchers/flowLogs, Dodaj powyższy kod JSON do sekcji Resources szablonu.


## <a name="creating-your-template"></a>Tworzenie szablonu

Jeśli używasz szablonów Azure Resource Manager po raz pierwszy, możesz dowiedzieć się więcej na ich temat, korzystając z poniższych linków.

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
* [Samouczek: Tworzenie i wdrażanie pierwszego szablonu Azure Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


Poniżej przedstawiono dwa przykłady kompletnych szablonów służących do konfigurowania dzienników przepływów sieciowej grupy zabezpieczeń.

**Przykład 1**: najprostsza wersja powyższego z parametrami o minimalnych wymaganiach. Poniższy szablon umożliwia sieciowej grupy zabezpieczeń dzienników przepływów na docelowym sieciowej grupy zabezpieczeń i zapisuje je na danym koncie magazynu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Nazwa zasobu ma format "nadrzędny Resource_Child zasobu". W tym miejscu zasób nadrzędny jest wystąpieniem Network Watcher regionalnym (format: NetworkWatcher_RegionName. Przykład: NetworkWatcher_centraluseuap)
> * Element targetresourceid jest IDENTYFIKATORem zasobu docelowej sieciowej grupy zabezpieczeń
> * storageId to identyfikator zasobu docelowego konta magazynu

**Przykład 2**: następujące szablony umożliwiające włączenie dzienników przepływu sieciowej grupy zabezpieczeń (wersja 2) z zachowaniem przez 5 dni. Włączanie Analiza ruchu z interwałem przetwarzania wynoszącym 10 minut.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {
          "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
          }
        },
        "retentionPolicy": {
          "days": 5,
          "enabled": true
        },
        "format": {
          "type": "JSON",
          "version": 2          
        }
      }
    }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Wdrażanie szablonu usługi Azure Resource Manager

W tym samouczku przyjęto założenie, że masz istniejącą grupę zasobów i sieciowej grupy zabezpieczeń można włączyć logowanie do przepływu.
Każdy z powyższych przykładowych szablonów można zapisać lokalnie jako `azuredeploy.json` . Zaktualizuj wartości właściwości, tak aby wskazywały na prawidłowe zasoby w subskrypcji.

Aby wdrożyć szablon, uruchom następujące polecenie w programie PowerShell.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Powyższe polecenia służą do wdrażania zasobu w grupie zasobów NetworkWatcherRG, a nie do grupy zasobów zawierającej sieciowej grupy zabezpieczeń


## <a name="verifying-your-deployment"></a>Weryfikowanie wdrożenia

Istnieje kilka sposobów, aby sprawdzić, czy wdrożenie zakończyło się pomyślnie. W konsoli programu PowerShell powinna zostać wyświetlona wartość "ProvisioningState" ("powodzenie"). Ponadto możesz odwiedzić [stronę portalu sieciowej grupy zabezpieczeń Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) , aby potwierdzić zmiany. Jeśli wystąpiły problemy ze wdrożeniem, zapoznaj się z tematem [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="deleting-your-resource"></a>Usuwanie zasobu
System Azure umożliwia usunięcie zasobów w trybie wdrożenia "Complete". Aby usunąć zasób dzienniki przepływów, określ wdrożenie w trybie kompletnym bez uwzględniania zasobu, który chcesz usunąć. Przeczytaj więcej na temat [całego trybu wdrożenia](../azure-resource-manager/templates/deployment-modes.md#complete-mode)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizować dane przepływu usługi sieciowej grupy zabezpieczeń przy użyciu:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Narzędzia Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Analiza ruchu platformy Azure](./traffic-analytics.md)