---
title: Tworzenie centrum powiadomień platformy Azure przy użyciu szablonu Azure Resource Manager
description: Dowiedz się, jak utworzyć centrum powiadomień platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 08/04/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: 2165351bee4ee260e768c10b23e26bf095779cad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88684647"
---
# <a name="quickstart-create-a-notification-hub-using-an-arm-template"></a>Szybki Start: tworzenie centrum powiadomień przy użyciu szablonu ARM

Usługa Azure Notification Hubs oferuje łatwy w użyciu i skalowany aparat wypychania, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle itp.) z dowolnego zaplecza (w chmurze lub lokalnie). Aby uzyskać więcej informacji o usłudze, zobacz artykuł [co to jest usługa Azure Notification Hubs](notification-hubs-push-notification-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten przewodnik Szybki Start używa szablonu Azure Resource Manager (szablon ARM) do utworzenia przestrzeni nazw platformy Azure Notification Hubs, a centrum powiadomień o nazwie **MyHub** w tej przestrzeni nazw.

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-notification-hub/).

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json":::

* [Microsoft. NotificationHubs/przestrzenie nazw](/azure/templates/microsoft.notificationhubs/namespaces)
* [Microsoft. NotificationHubs/przestrzenie nazw/notificationHubs](/azure/templates/microsoft.notificationhubs/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon przyjmuje nazwę przestrzeni nazw Notification Hubs jako parametr. Następnie szablon tworzy przestrzeń nazw o tej nazwie i centrum powiadomień o nazwie **MyHub** w tej przestrzeni nazw.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Możesz użyć Azure Portal do sprawdzenia wdrożonych zasobów lub użyć interfejsu wiersza polecenia platformy Azure lub skryptu Azure PowerShell, aby wyświetlić listę wdrożonych Notification Hubs przestrzeni nazw i centrum:

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już konieczna, usuń ją, co spowoduje usunięcie zasobów z grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
