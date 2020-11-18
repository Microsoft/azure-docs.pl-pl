---
title: 'Szybki Start: Tworzenie usługi Azure Signal Service — szablon ARM'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć usługę Azure Signal Service przy użyciu szablonu Azure Resource Manager (szablon ARM).
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: zhshang
ms.date: 10/02/2020
ms.openlocfilehash: 4ab029048b37a4dcb44ef405249dcb9e20de70cf
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841620"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Szybki Start: korzystanie z szablonu ARM w celu wdrożenia usługi Azure Signal Service

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia usługi Azure Signal Service. Usługę Azure Signal można wdrożyć za pomocą Azure Portal, PowerShell lub interfejsu wiersza polecenia.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w Azure Portal po zalogowaniu się.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Przycisk służący do wdrażania usługi Azure Signal platform na platformie Azure przy użyciu szablonu ARM w Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).
* Jeśli chcesz uruchomić kod lokalnie, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).
* Jeśli chcesz uruchomić kod lokalnie:
    * Powłoka bash (na przykład git bash, która jest zawarta w narzędziu [git dla systemu Windows](https://gitforwindows.org)).
    * [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

Szablon definiuje jeden zasób platformy Azure:

* [**Microsoft. SignalRService/sygnalizujący**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Wdrażanie szablonu

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wybierz następujący link, aby wdrożyć usługę Azure Signal Service przy użyciu szablonu ARM w Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Przycisk służący do wdrażania usługi Azure Signal platform na platformie Azure przy użyciu szablonu ARM w Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

Na stronie **wdrażanie usługi Azure Signal Service** :

1. Jeśli chcesz, Zmień domyślną **subskrypcję** .

2. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, wprowadź nazwę nowej grupy zasobów, a następnie wybierz pozycję **OK**.

3. Jeśli utworzono nową grupę zasobów, wybierz **region** dla grupy zasobów.

4. Jeśli chcesz, wprowadź nową **nazwę** i **lokalizację** (na przykład **Eastus2**) usługi Azure Signal Service. Jeśli nazwa nie zostanie określona, jest generowana automatycznie. Lokalizacja usługi Azure Signal Service może być taka sama jak lub różna od regionu grupy zasobów. Jeśli lokalizacja nie zostanie określona, zostanie ona ustawiona na ten sam region, w którym znajduje się grupa zasobów.

5. Wybierz **warstwę cenową** (**Free_F1** lub **Standard_S1**), wprowadź **pojemność** (liczbę jednostek sygnalizujących), a następnie wybierz **tryb usługi** **domyślnej** (wymaga serwera Hub), **bezserwerowe** (nie zezwala na żadne połączenie z serwerem) ani **klasyczny** (kierowany do serwera Hub tylko wtedy, gdy koncentrator ma połączenie z serwerem). Następnie zdecyduj, czy **włączyć dzienniki łączności** , czy **włączyć dzienniki obsługi komunikatów**.

    > [!NOTE]
    > W przypadku warstwy cenowej **Free_F1** pojemność jest ograniczona do 1 jednostki.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Zrzut ekranu przedstawiający szablon ARM służący do tworzenia usługi Azure Signal w Azure Portal.":::

6. Wybierz pozycję **Przejrzyj i utwórz**.

7. Przeczytaj warunki i postanowienia, a następnie wybierz pozycję **Utwórz**.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

> [!NOTE]
> Jeśli chcesz uruchomić skrypty programu PowerShell lokalnie, najpierw wprowadź, `Connect-AzAccount` Aby skonfigurować swoje poświadczenia platformy Azure.

Użyj poniższego kodu, aby wdrożyć usługę Azure Signal przy użyciu szablonu ARM. Kod pyta o następujące elementy:

* Nazwa i region nowej usługi Azure sygnalizującej
* Nazwa i region nowej grupy zasobów
* Warstwa cenowa platformy Azure (**Free_F1** lub **Standard_S1**)
* Pojemność jednostki sygnalizującej (1, 2, 5, 10, 20, 50 lub 100)
  > [!NOTE]
  > W przypadku warstwy cenowej **Free_F1** pojemność jest ograniczona do 1 jednostki.
* Tryb usługi: **Domyślnie** wymagany jest serwer centralny, **bezserwerowy** , aby nie zezwalać na połączenie z serwerem, lub **klasyczny** do kierowania z serwerem centralnym tylko wtedy, gdy koncentrator ma połączenie z serwerem
* Czy włączyć dzienniki dla połączeń lub komunikatów (**true** lub **false**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Użyj poniższego kodu, aby wdrożyć usługę Azure Signal przy użyciu szablonu ARM. Kod pyta o następujące elementy:

* Nazwa i region nowej usługi Azure sygnalizującej
* Nazwa i region nowej grupy zasobów
* Warstwa cenowa platformy Azure (**Free_F1** lub **Standard_S1**)
* Pojemność jednostki sygnalizującej (1, 2, 5, 10, 20, 50 lub 100)
    > [!NOTE]
    > W przypadku warstwy cenowej **Free_F1** pojemność jest ograniczona do 1 jednostki.
* Tryb usługi: **Domyślnie** wymagany jest serwer centralny, **bezserwerowy** , aby nie zezwalać na połączenie z serwerem, lub **klasyczny** do kierowania z serwerem centralnym tylko wtedy, gdy koncentrator ma połączenie z serwerem
* Czy włączyć dzienniki dla połączeń lub komunikatów (**true** lub **false**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Wdrożenie może potrwać kilka minut. Zwróć uwagę na nazwy usługi Azure sygnalizującej i grupy zasobów, które są używane do późniejszego przeglądania wdrożonych zasobów.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby zapoznać się z omówieniem nowej usługi Azure Signal:

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **sygnalizujący**.

2. Na liście sygnalizujący wybierz nową usługę. Zostanie wyświetlona strona **Przegląd** nowej usługi Azure Signal.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Uruchom Poniższy kod interaktywny, aby wyświetlić szczegółowe informacje o usłudze Azure Signal Service. Musisz wprowadzić nazwę nowej usługi i grupy zasobów.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Uruchom Poniższy kod interaktywny, aby wyświetlić szczegółowe informacje o usłudze Azure Signal Service. Musisz wprowadzić nazwę nowej usługi i grupy zasobów.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń grupę zasobów, która spowoduje usunięcie zasobów z grupy zasobów.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **grupy zasobów**.

2. Na liście Grupa zasobów wybierz nazwę grupy zasobów.

3. Na stronie **Przegląd** w grupie zasobów wybierz pozycję **Usuń grupę zasobów**.

4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu usługi ARM, zobacz:

> [!div class="nextstepaction"]
> [ Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
