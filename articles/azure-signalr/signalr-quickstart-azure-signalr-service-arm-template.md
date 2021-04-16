---
title: 'Szybki start: tworzenie aplikacji Azure SignalR Service — szablon usługi ARM'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć Azure SignalR Service szablonu Azure Resource Manager (szablonu USŁUGI ARM).
author: sffamily
ms.author: zhshang
ms.date: 10/02/2020
ms.topic: quickstart
ms.service: signalr
ms.custom:
- subject-armqs
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 075915f2a06e29eb5b80425913c6e7a2c476ff99
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536511"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Szybki start: wdrażanie aplikacji przy użyciu szablonu usługi ARM Azure SignalR Service

W tym przewodniku Szybki start opisano sposób Azure Resource Manager szablonu usługi Arm w celu utworzenia Azure SignalR Service. Aplikację można wdrożyć Azure SignalR Service za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w Azure Portal po zalogowaniu.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Przycisk do wdrażania Azure SignalR Service na platformie Azure przy użyciu szablonu usługi ARM w Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/)
* Jeśli chcesz uruchomić kod lokalnie, uruchom [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/)
* Jeśli chcesz uruchomić kod lokalnie:
    * Powłoka Bash (na przykład Git Bash, która jest zawarta w [usłudze Git dla systemu Windows).](https://gitforwindows.org)
    * [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

Szablon definiuje jeden zasób platformy Azure:

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wybierz następujący link, aby wdrożyć aplikację Azure SignalR Service użyciu szablonu usługi ARM w Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Przycisk wdrażania Azure SignalR Service na platformie Azure przy użyciu szablonu USŁUGI ARM w Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

Na stronie **Deploy an Azure SignalR Service (Wdrażanie** Azure SignalR Service aplikacji):

1. Jeśli chcesz, zmień wartość **domyślną subskrypcji.**

2. W **przypadku grupy zasobów** wybierz pozycję Utwórz **nową,** wprowadź nazwę nowej grupy zasobów, a następnie wybierz przycisk **OK.**

3. Jeśli utworzono nową grupę zasobów, wybierz **region** dla grupy zasobów.

4. Jeśli chcesz, wprowadź nową **nazwę i** **lokalizację** (na przykład **eastus2)** Azure SignalR Service. Jeśli nie określisz nazwy, zostanie ona wygenerowana automatycznie. Lokalizacja dla Azure SignalR Service może być taka sama jak lub inna niż region grupy zasobów. Jeśli nie określisz lokalizacji, zostanie ona ustawiona na ten sam region co grupa zasobów.

5. Wybierz  warstwę cenową **(Free_F1** lub **Standard_S1), wprowadź** pojemność **(liczbę** jednostek SignalR) i wybierz domyślny tryb usługi **(wymaga** serwera **koncentratora),** bez serwera (nie zezwala na żadne połączenie z serwerem) lub klasyczny **(kierowane** do serwera koncentratora tylko wtedy, gdy centrum ma połączenie z serwerem).  Następnie wybierz, czy włączyć **dzienniki łączności,** czy **włączyć dzienniki obsługi komunikatów.**

    > [!NOTE]
    > W przypadku **Free_F1** cenowej pojemność jest ograniczona do 1 jednostki.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Zrzut ekranu przedstawiający szablon usługi ARM do tworzenia Azure SignalR Service w Azure Portal.":::

6. Wybierz pozycję **Przejrzyj i utwórz**.

7. Przeczytaj warunki i postanowienia, a następnie wybierz pozycję **Utwórz.**

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

> [!NOTE]
> Jeśli chcesz uruchomić skrypty programu PowerShell lokalnie, najpierw wprowadź , aby `Connect-AzAccount` skonfigurować poświadczenia platformy Azure.

Użyj poniższego kodu, aby wdrożyć Azure SignalR Service przy użyciu szablonu usługi ARM. Kod wyświetli monit o następujące elementy:

* Nazwa i region nowej Azure SignalR Service
* Nazwa i region nowej grupy zasobów
* Warstwa cenowa platformy Azure **(Free_F1** lub **Standard_S1**)
* Pojemność jednostki SignalR (1, 2, 5, 10, 20, 50 lub 100)
  > [!NOTE]
  > W przypadku **Free_F1** cenowej pojemność jest ograniczona do 1 jednostki.
* Tryb **usługi:** domyślnie wymagaj serwera koncentratora, bez serwera, aby  nie zezwalać na żadne połączenie serwera, lub klasyczny do przekierowania do serwera koncentratora tylko wtedy, gdy koncentrator ma połączenie z serwerem 
* Czy włączyć dzienniki dla łączności lub obsługi komunikatów **(prawda** czy **fałsz)**

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

Użyj poniższego kodu, aby wdrożyć Azure SignalR Service przy użyciu szablonu usługi ARM. Kod wyświetli monit o następujące elementy:

* Nazwa i region nowego Azure SignalR Service
* Nazwa i region nowej grupy zasobów
* Warstwa cenowa platformy Azure **(Free_F1** lub **Standard_S1**)
* Pojemność jednostki SignalR (1, 2, 5, 10, 20, 50 lub 100)
    > [!NOTE]
    > W przypadku **Free_F1** cenowej pojemność jest ograniczona do 1 jednostki.
* Tryb **usługi:** domyślnie wymagaj serwera koncentratora, bez serwera, aby  nie zezwalać na żadne połączenie serwera, lub klasyczny do przekierowania do serwera koncentratora tylko wtedy, gdy koncentrator ma połączenie z serwerem 
* Czy włączyć dzienniki dla łączności lub obsługi komunikatów **(prawda** czy **fałsz)**

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
> Wdrożenie może potrwać kilka minut. Zanotuj nazwy Azure SignalR Service i grupę zasobów, których użyjemy do późniejszego przejrzenia wdrożonych zasobów.

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby wyświetlić omówienie nowych Azure SignalR Service:

1. W [Azure Portal](https://portal.azure.com)wyszukaj i wybierz pozycję **SignalR.**

2. Na liście SignalR wybierz nową usługę. Zostanie **wyświetlona** strona Przegląd Azure SignalR Service nowej aplikacji.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje o Azure SignalR Service. Musisz wprowadzić nazwę nowej usługi i grupę zasobów.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje o Azure SignalR Service. Musisz wprowadzić nazwę nowej usługi i grupę zasobów.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją, co spowoduje usunięcie zasobów w grupie zasobów.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na stronie [Azure Portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Grupy zasobów.**

2. Na liście grup zasobów wybierz nazwę grupy zasobów.

3. Na stronie **Przegląd** grupy zasobów wybierz pozycję **Usuń grupę zasobów.**

4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń.**

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

Aby uzyskać samouczek krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu usługi ARM, zobacz:

> [!div class="nextstepaction"]
> [ Samouczek: tworzenie i wdrażanie pierwszego szablonu usługi ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
