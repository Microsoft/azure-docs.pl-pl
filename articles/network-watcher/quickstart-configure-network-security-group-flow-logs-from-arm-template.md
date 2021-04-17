---
title: 'Szybki start: konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu szablonu Azure Resource Manager (szablonu usługi ARM)'
description: Dowiedz się, jak programowo włączyć dzienniki przepływu sieciowej grupy zabezpieczeń przy użyciu szablonu Azure Resource Manager (szablonu USŁUGI ARM) i Azure PowerShell.
services: network-watcher
author: damendo
ms.author: damendo
ms.date: 01/07/2021
ms.topic: quickstart
ms.service: network-watcher
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: df0ccb5bf5ecd60d80526085983e35abf58e9966
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532438"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Szybki start: konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu szablonu usługi ARM

Z tego przewodnika Szybki start dowiesz się, jak włączyć dzienniki [](../azure-resource-manager/management/overview.md) przepływu sieciowej grupy zabezpieczeń [przy](network-watcher-nsg-flow-logging-overview.md) użyciu szablonu Azure Resource Manager (szablonu USŁUGI ARM) i Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zaczniemy od przeglądu właściwości obiektu dziennika przepływu sieciowej grupy zabezpieczeń. Dostarczamy przykładowe szablony. Następnie użyjemy lokalnego Azure PowerShell do wdrożenia szablonu.

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon, który używamy w tym przewodniku Szybki start, pochodzi z szablonów [szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Te zasoby są zdefiniowane w szablonie:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Obiekt dzienników przepływu sieciowej grupy zabezpieczeń

Poniższy kod przedstawia obiekt dzienników przepływu sieciowej grupy zabezpieczeń i jego parametry. Aby utworzyć `Microsoft.Network/networkWatchers/flowLogs` zasób, dodaj ten kod do sekcji resources szablonu:

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

Aby uzyskać pełne omówienie właściwości obiektu dzienników przepływu sieciowej grupy zabezpieczeń, zobacz [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Tworzenie szablonu

Jeśli używasz szablonów usługi ARM po raz pierwszy, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o szablonach usługi ARM:

- [Wdrażanie zasobów przy użyciu szablonów usługi ARM i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
- [Samouczek: tworzenie i wdrażanie pierwszego szablonu usługi ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Poniższy przykład to kompletny szablon. Jest to również najprostsza wersja szablonu. Przykład zawiera minimalne parametry, które są przekazywane do skonfigurowania dzienników przepływu sieciowej organizacji zabezpieczeń. Aby uzyskać więcej przykładów, zobacz artykuł z omówieniem [Configure NSG flow logs from an Azure Resource Manager template](network-watcher-nsg-flow-logging-azure-resource-manager.md)(Konfigurowanie dzienników przepływu sieciowej Azure Resource Manager sieciowej).

### <a name="example"></a>Przykład

Poniższy szablon umożliwia włączenie dzienników przepływu dla sieciowej organizacji zabezpieczeń, a następnie przechowywanie dzienników na określonym koncie magazynu:

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
> - Nazwa zasobu używa formatu _ParentResource_ChildResource_. W naszym przykładzie zasób nadrzędny to regionalne wystąpienie Network Watcher Azure:
>    - **Format:** NetworkWatcher_RegionName
>    - **Przykład:** NetworkWatcher_centraluseuap
> - `targetResourceId` to identyfikator zasobu docelowej sieciowej grupy zasobów.
> - `storageId` to identyfikator zasobu docelowego konta magazynu.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

W tym samouczku przyjęto założenie, że masz istniejącą grupę zasobów i sieciową grupę zasobów, dla których możesz włączyć rejestrowanie przepływu.

Możesz zapisać dowolne przykładowe szablony, które są wyświetlane w tym artykule lokalnie, *azuredeploy.jsna stronie*. Zaktualizuj wartości właściwości, aby wskazać prawidłowe zasoby w subskrypcji.

Aby wdrożyć szablon, uruchom następujące polecenie w Azure PowerShell:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Te polecenia wdrażają zasób w przykładowej grupie zasobów NetworkWatcherRG, a nie w grupie zasobów zawierającej sieciową grupę zasobów.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Dostępne są dwie opcje, aby sprawdzić, czy wdrożenie zakończyło się pomyślnie:

- Konsola programu PowerShell będzie `ProvisioningState` wyświetlać się jako `Succeeded` .
- Przejdź do strony [portalu dzienników przepływu sieciowej organizacji zabezpieczeń,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) aby potwierdzić zmiany.

Jeśli wystąpiły problemy z wdrożeniem, zobacz [Troubleshoot common Azure deployment errors with Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)(Rozwiązywanie typowych błędów wdrażania platformy Azure za pomocą Azure Resource Manager ).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby platformy Azure można usunąć przy użyciu pełnego trybu wdrażania. Aby usunąć zasób dzienników przepływu, określ wdrożenie w trybie kompletnym bez uwzględnienia zasobu, który chcesz usunąć. Przeczytaj więcej na [temat pełnego trybu wdrażania.](../azure-resource-manager/templates/deployment-modes.md#complete-mode)

Możesz również wyłączyć dziennik przepływu sieciowej Azure Portal:

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz pozycję **Wszystkie usługi**. W polu **Filtr** wprowadź wartość **network watcher**. W wynikach wyszukiwania wybierz pozycję **Network Watcher**.
1. W **obszarze Dzienniki** wybierz pozycję Dzienniki **przepływu sieciowej organizacji zabezpieczeń.**
1. Na liście sieciowych sieciowych sieciowych sieci wybierz sieciową listę zabezpieczeń, dla której chcesz wyłączyć dzienniki przepływu.
1. W **obszarze Ustawienia dzienników przepływu** wybierz pozycję **Wył.**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób włączania dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu szablonu usługi ARM. Następnie dowiedz się, jak wizualizować dane przepływu sieciowej sieci przy użyciu jednej z tych opcji:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Narzędzia typu open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Analiza ruchu](traffic-analytics.md)
