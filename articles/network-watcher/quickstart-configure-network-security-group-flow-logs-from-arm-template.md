---
title: 'Szybki Start: Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu szablonu Azure Resource Manager'
description: Dowiedz się, jak w sposób programowy włączyć dzienniki przepływu sieciowej grupy zabezpieczeń przy użyciu szablonu Azure Resource Manager i Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87986321"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Szybki Start: Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń z poziomu szablonu ARM

W tym przewodniku Szybki Start [sieciowej grupy zabezpieczeń dzienniki przepływu](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) usługi przy użyciu szablonu [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) (szablonu ARM) i Azure PowerShell. 

Zaczynamy od udostępnienia przeglądu właściwości obiektu dziennika przepływu sieciowej grupy zabezpieczeń, po którym następuje kilka przykładowego szablonu. Następnie wdrażamy szablon przy użyciu lokalnego wystąpienia programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="nsg-flow-logs-object"></a>Obiekt dzienników przepływu sieciowej grupy zabezpieczeń

Obiekt dzienników przepływu sieciowej grupy zabezpieczeń ze wszystkimi parametrami jest przedstawiony poniżej.
Pełny przegląd właściwości można znaleźć w [dokumentacji szablonów przepływu sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Samouczek: Tworzenie i wdrażanie pierwszego szablonu Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Poniższy przykład pełnego szablonu to najprostsza wersja z minimalnymi parametrami przekazaną do skonfigurowania dzienników przepływów sieciowej grupy zabezpieczeń. Aby uzyskać więcej przykładów, przejdź do tego [linku](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager).

**Przykład**: Poniższy szablon umożliwia sieciowej grupy zabezpieczeń dzienników przepływów na docelowym sieciowej grupy zabezpieczeń i zapisuje je na danym koncie magazynu.

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


## <a name="deploying-your-azure-resource-manager-template"></a>Wdrażanie szablonu usługi Azure Resource Manager

W tym samouczku przyjęto założenie, że masz istniejącą grupę zasobów i sieciowej grupy zabezpieczeń można włączyć logowanie do przepływu.
Każdy z powyższych przykładowych szablonów można zapisać lokalnie jako `azuredeploy.json` . Zaktualizuj wartości właściwości, tak aby wskazywały na prawidłowe zasoby w subskrypcji.

Aby wdrożyć szablon, uruchom następujące polecenie w programie PowerShell.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Powyższe polecenia służą do wdrażania zasobu w grupie zasobów NetworkWatcherRG, a nie do grupy zasobów zawierającej sieciowej grupy zabezpieczeń


## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Istnieje kilka sposobów, aby sprawdzić, czy wdrożenie zakończyło się pomyślnie. W konsoli programu PowerShell powinna zostać wyświetlona wartość "ProvisioningState" ("powodzenie"). Ponadto możesz odwiedzić [stronę portalu sieciowej grupy zabezpieczeń Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) , aby potwierdzić zmiany. Jeśli wystąpiły problemy ze wdrożeniem, zapoznaj się z tematem [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Usuwanie zasobu
System Azure umożliwia usunięcie zasobów w trybie wdrożenia "Complete". Aby usunąć zasób dzienniki przepływów, określ wdrożenie w trybie kompletnym bez uwzględniania zasobu, który chcesz usunąć. Przeczytaj więcej na temat [całego trybu wdrożenia](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

Alternatywnie można wyłączyć dziennik przepływu sieciowej grupy zabezpieczeń z Azure Portal zgodnie z poniższymi krokami:
1. Logowanie do witryny Azure Portal
2. W lewym górnym rogu portalu wybierz pozycję **Wszystkie usługi**. W polu **Filtr** wpisz ciąg *Network Watcher*. Gdy w wynikach wyszukiwania pojawi się nazwa **Network Watcher**, wybierz ją.
3. W obszarze **dzienniki**wybierz pozycję **dzienniki przepływu sieciowej grupy zabezpieczeń**
4. Z listy sieciowych grup zabezpieczeń wybierz sieciowej grupy zabezpieczeń, dla którego chcesz wyłączyć dzienniki przepływów
5. W obszarze **Ustawienia dzienników przepływów**Ustaw stan dziennika przepływy jako **wyłączony**
6. Przewiń w dół i wybierz pozycję **Zapisz**

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start włączono dzienniki przepływu sieciowej grupy zabezpieczeń. Teraz musisz dowiedzieć się, jak wizualizować dane przepływu usługi sieciowej grupy zabezpieczeń przy użyciu: 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Narzędzia Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Analiza ruchu platformy Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
