---
title: 'Szybki Start: Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu szablonu Azure Resource Manager'
description: Dowiedz się, jak włączać dzienniki przepływu sieciowej grupy zabezpieczeń programowo przy użyciu szablonu Azure Resource Manager (szablon ARM) i Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042749"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Szybki Start: Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu szablonu ARM

W tym przewodniku szybki start można włączyć [dzienniki przepływu sieciowej grupy zabezpieczeń](network-watcher-nsg-flow-logging-overview.md) przy użyciu szablonu [Azure Resource Manager](../azure-resource-manager/management/overview.md) (szablon ARM) i Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zaczynamy od udostępnienia przeglądu właściwości obiektu dziennika przepływu sieciowej grupy zabezpieczeń, po którym następuje kilka przykładowego szablonu. Następnie wdrażamy szablon przy użyciu lokalnego wystąpienia programu PowerShell.

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. resources/Deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Obiekt dzienników przepływu sieciowej grupy zabezpieczeń

Obiekt dzienników przepływu sieciowej grupy zabezpieczeń ze wszystkimi parametrami jest przedstawiony poniżej. Aby uzyskać pełny przegląd właściwości, zobacz [Microsoft. Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Aby utworzyć `Microsoft.Network/networkWatchers/flowLogs` zasób, Dodaj powyższy kod JSON do sekcji Resources szablonu.

## <a name="creating-your-template"></a>Tworzenie szablonu

Jeśli używasz szablonów usługi ARM po raz pierwszy, możesz dowiedzieć się więcej na ich temat, korzystając z poniższych linków.

- [Wdrażanie zasobów za pomocą szablonów ARM i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Poniższy przykład pełnego szablonu to najprostsza wersja z minimalnymi parametrami przekazaną do skonfigurowania dzienników przepływów sieciowej grupy zabezpieczeń. Aby uzyskać więcej przykładów, przejdź do [przewodnika krok po kroku](network-watcher-nsg-flow-logging-azure-resource-manager.md).

**Przykład** : Poniższy szablon umożliwia sieciowej grupy zabezpieczeń dzienników przepływów na docelowym sieciowej grupy zabezpieczeń i zapisuje je na danym koncie magazynu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - Nazwa zasobu ma format _nadrzędny Resource_Child zasób_ . W tym miejscu zasób nadrzędny jest wystąpieniem Network Watcher regionalnym (format: NetworkWatcher_RegionName. Przykład: NetworkWatcher_centraluseuap)
> - `targetResourceId` jest IDENTYFIKATORem zasobu docelowej sieciowej grupy zabezpieczeń.
> - `storageId` jest IDENTYFIKATORem zasobu docelowego konta magazynu.

## <a name="deploy-the-template"></a>Wdrażanie szablonu

W tym samouczku przyjęto założenie, że masz istniejącą grupę zasobów i sieciowej grupy zabezpieczeń można włączyć logowanie do przepływu.
Każdy z powyższych przykładowych szablonów można zapisać lokalnie jako `azuredeploy.json` . Zaktualizuj wartości właściwości, tak aby wskazywały na prawidłowe zasoby w subskrypcji.

Aby wdrożyć szablon, uruchom następujące polecenie w programie PowerShell.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Powyższe polecenia służą do wdrażania zasobu w grupie zasobów NetworkWatcherRG, a nie do grupy zasobów zawierającej sieciowej grupy zabezpieczeń

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Istnieje kilka sposobów, aby sprawdzić, czy wdrożenie zakończyło się pomyślnie. W konsoli programu PowerShell powinna być wyświetlana wartość `ProvisioningState` `Succeeded` . Ponadto możesz odwiedzić [stronę portalu sieciowej grupy zabezpieczeń Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) , aby potwierdzić zmiany. Jeśli wystąpiły problemy ze wdrożeniem, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

System Azure umożliwia usunięcie zasobów w `Complete` trybie wdrożenia. Aby usunąć zasób dzienniki przepływów, określ wdrożenie w `Complete` trybie bez uwzględniania zasobu, który chcesz usunąć. Przeczytaj więcej na temat [całego trybu wdrożenia](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Alternatywnie można wyłączyć dziennik przepływu sieciowej grupy zabezpieczeń z Azure Portal zgodnie z poniższymi krokami:

1. Logowanie do witryny Azure Portal
1. W lewym górnym rogu portalu wybierz pozycję **Wszystkie usługi** . W polu **Filtr** wpisz ciąg _Network Watcher_ . Gdy w wynikach wyszukiwania pojawi się nazwa **Network Watcher** , wybierz ją.
1. W obszarze **dzienniki** wybierz pozycję **dzienniki przepływu sieciowej grupy zabezpieczeń** .
1. Z listy sieciowych grup zabezpieczeń wybierz sieciowej grupy zabezpieczeń, dla którego chcesz wyłączyć dzienniki przepływów.
1. W obszarze **Ustawienia dzienników przepływu** Ustaw stan dziennika przepływy jako **wyłączony** .
1. Przewiń w dół i wybierz pozycję **Zapisz** .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start włączono dzienniki przepływu sieciowej grupy zabezpieczeń. Teraz musisz dowiedzieć się, jak wizualizować dane przepływu usługi sieciowej grupy zabezpieczeń przy użyciu:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Narzędzia Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Analiza ruchu platformy Azure](traffic-analytics.md)
