---
title: Tworzenie centrum powiadomień platformy Azure przy użyciu Azure Resource Manager szablonu
description: Dowiedz się, jak utworzyć centrum powiadomień platformy Azure przy użyciu szablonu Azure Resource Manager (szablonu usługi ARM).
services: notification-hubs
author: sethmanheim
ms.author: sethm
ms.reviewer: thsomasu
ms.date: 08/04/2020
ms.lastreviewed: 05/15/2020
ms.topic: quickstart
ms.service: notification-hubs
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: a328d6b8942c3209e13dc91a2fb892e98e3016f6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533475"
---
# <a name="quickstart-create-a-notification-hub-using-an-arm-template"></a>Szybki start: tworzenie centrum powiadomień przy użyciu szablonu usługi ARM

Usługa Azure Notification Hubs udostępnia łatwy w użyciu i skalowany w zewnątrz aparat wypychania, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle itp.) z dowolnego zaplecza (w chmurze lub lokalnie). Aby uzyskać więcej informacji na temat usługi, zobacz [Co to jest usługa Azure Notification Hubs](notification-hubs-push-notification-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

W tym przewodniku Szybki start Azure Resource Manager szablonu usługi Azure Notification Hubs oraz centrum powiadomień o nazwie **MyHub** w ramach tej przestrzeni nazw.

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-notification-hub/).

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json":::

* [Microsoft.NotificationHubs/namespaces](/azure/templates/microsoft.notificationhubs/namespaces)
* [Microsoft.NotificationHubs/namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon przyjmuje nazwę Notification Hubs nazw jako parametr. Następnie szablon tworzy przestrzeń nazw o tej nazwie i centrum powiadomień o nazwie **MyHub** w ramach tej przestrzeni nazw.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Możesz użyć narzędzia Azure Portal, aby sprawdzić wdrożone zasoby, albo użyć interfejsu wiersza polecenia platformy Azure lub skryptu Azure PowerShell, aby wyświetlić listę wdrożonych Notification Hubs przestrzeni nazw i centrum:

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

Gdy grupa zasobów nie będzie już potrzebna, usuń ją, co spowoduje usunięcie zasobów w grupie zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać samouczek krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: tworzenie i wdrażanie pierwszego szablonu usługi ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
