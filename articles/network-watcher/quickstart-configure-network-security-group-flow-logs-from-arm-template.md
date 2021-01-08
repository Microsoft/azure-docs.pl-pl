---
title: 'Szybki Start: Konfigurowanie dzienników przepływu sieciowych grup zabezpieczeń przy użyciu szablonu Azure Resource Manager (szablon ARM)'
description: Dowiedz się, jak włączyć obsługę dzienników usługi Network Security Group (sieciowej grupy zabezpieczeń) przy użyciu szablonu Azure Resource Manager (szablonu ARM) i Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the network security group flow logs by using an Azure Resource Manager template.
ms.service: network-watcher
ms.topic: quickstart
ms.date: 01/07/2021
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: ded7b24461fdcdbc3d020a487cafc20620633097
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019724"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Szybki Start: Konfigurowanie dzienników przepływu sieciowych grup zabezpieczeń przy użyciu szablonu ARM

W tym przewodniku szybki start dowiesz się, jak włączyć [dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](network-watcher-nsg-flow-logging-overview.md) przy użyciu szablonu [Azure Resource Manager](../azure-resource-manager/management/overview.md) (szablon ARM) i Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zaczynamy od przeglądu właściwości obiektu dziennika przepływu sieciowej grupy zabezpieczeń. Udostępniamy przykładowe szablony. Następnie użyjemy lokalnego Azure PowerShell wystąpienia do wdrożenia szablonu.

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Te zasoby są zdefiniowane w szablonie:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. resources/Deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Obiekt dzienników przepływu sieciowej grupy zabezpieczeń

Poniższy kod przedstawia obiekt dziennika przepływu sieciowej grupy zabezpieczeń i jego parametry. Aby utworzyć `Microsoft.Network/networkWatchers/flowLogs` zasób, Dodaj ten kod do sekcji Resources szablonu:

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

Aby uzyskać pełny przegląd właściwości obiektów dzienników przepływu sieciowej grupy zabezpieczeń, zobacz [Microsoft. Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Utwórz szablon

Jeśli używasz szablonów ARM po raz pierwszy, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o szablonach usługi ARM:

- [Wdrażanie zasobów za pomocą szablonów ARM i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Poniższy przykład jest kompletnym szablonem. Jest to również najprostsza wersja szablonu. Przykład zawiera minimalne parametry, które są przesyłane w celu skonfigurowania dzienników przepływu sieciowej grupy zabezpieczeń. Aby uzyskać więcej przykładów, zobacz artykuł Omówienie [Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń na podstawie szablonu Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md).

### <a name="example"></a>Przykład

Poniższy szablon umożliwia wykonywanie dzienników przepływu dla programu sieciowej grupy zabezpieczeń, a następnie przechowywanie dzienników na określonym koncie magazynu:

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
> - Nazwa zasobu używa formatu _ParentResource_ChildResource_. W naszym przykładzie zasób nadrzędny jest regionalnym wystąpieniem usługi Azure Network Watcher:
>    - **Format**: NetworkWatcher_RegionName
>    - **Przykład**: NetworkWatcher_centraluseuap
> - `targetResourceId` jest IDENTYFIKATORem zasobu docelowej sieciowej grupy zabezpieczeń.
> - `storageId` jest IDENTYFIKATORem zasobu docelowego konta magazynu.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

W tym samouczku przyjęto założenie, że masz istniejącą grupę zasobów i sieciowej grupy zabezpieczeń, w której można włączyć logowanie do przepływu.

Wszystkie przykładowe szablony, które są wyświetlane w tym artykule, można zapisać lokalnie jako *azuredeploy.js*. Zaktualizuj wartości właściwości, aby wskazywały na prawidłowe zasoby w ramach subskrypcji.

Aby wdrożyć szablon, uruchom następujące polecenie w Azure PowerShell:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Te polecenia służą do wdrażania zasobu w przykładowej grupie zasobów NetworkWatcherRG, a nie do grupy zasobów zawierającej sieciowej grupy zabezpieczeń.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Dostępne są dwie opcje, aby sprawdzić, czy wdrożenie zakończyło się pomyślnie:

- Konsola programu PowerShell pokazuje `ProvisioningState` jako `Succeeded` .
- Przejdź do [strony portalu sieciowej grupy zabezpieczeń Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) , aby potwierdzić zmiany. 

Jeśli wystąpiły problemy ze wdrożeniem, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby platformy Azure można usunąć, korzystając z trybu wdrożenia. Aby usunąć zasób dzienniki przepływów, określ wdrożenie w trybie kompletnym bez uwzględniania zasobu, który chcesz usunąć. Przeczytaj więcej na temat [kończenia trybu wdrożenia](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Dziennik przepływu sieciowej grupy zabezpieczeń można także wyłączyć w Azure Portal:

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz pozycję **Wszystkie usługi**. W polu **Filtr** wprowadź **obserwator sieciowy**. W wynikach wyszukiwania wybierz pozycję **Network Watcher**.
1. W obszarze **dzienniki** wybierz pozycję **dzienniki przepływu sieciowej grupy zabezpieczeń**.
1. Na liście sieciowych grup zabezpieczeń wybierz sieciowej grupy zabezpieczeń, dla którego chcesz wyłączyć dzienniki przepływów.
1. W obszarze **Ustawienia dzienników przepływu** wybierz pozycję **wyłączone**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób włączania dzienników usługi sieciowej grupy zabezpieczeń Flow przy użyciu szablonu usługi ARM. Następnie Dowiedz się, jak wizualizować dane przepływu usługi sieciowej grupy zabezpieczeń przy użyciu jednej z następujących opcji:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Narzędzia Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Analiza ruchu platformy Azure](traffic-analytics.md)
