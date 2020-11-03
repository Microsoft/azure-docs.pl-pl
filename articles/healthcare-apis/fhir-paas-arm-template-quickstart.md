---
title: 'Szybki Start: wdrażanie interfejsu API platformy Azure dla usługi FHIR przy użyciu szablonu ARM'
description: W tym przewodniku szybki start dowiesz się, jak wdrożyć interfejs API platformy Azure pod kątem szybkich zasobów współdziałania z opieką zdrowotną (FHIR®) przy użyciu szablonu Azure Resource Manager (szablon ARM).
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: cavoeg
ms.date: 10/06/2020
ms.openlocfilehash: c04bb82810bf55d6ac55b2697f5010896e0eb9b4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289293"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>Szybki Start: używanie szablonu ARM do wdrażania interfejsu API platformy Azure dla usługi FHIR

W tym przewodniku szybki start dowiesz się, jak używać szablonu Azure Resource Manager (szablon ARM) do wdrażania interfejsu API platformy Azure pod kątem szybkich zasobów współdziałania z opieką służbową (FHIR®). Interfejs API platformy Azure dla usługi FHIR można wdrożyć za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w Azure Portal po zalogowaniu się.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż usługę Azure API for FHIR przy użyciu szablonu ARM w Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).
* Jeśli chcesz uruchomić kod lokalnie, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).
* Jeśli chcesz uruchomić kod lokalnie:
    * Powłoka bash (na przykład git bash, która jest zawarta w narzędziu [git dla systemu Windows](https://gitforwindows.org)).
    * [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/).

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

Szablon definiuje jeden zasób platformy Azure:

* **Microsoft. HealthcareApis/usługi**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>Wdrażanie szablonu

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wybierz następujący link, aby wdrożyć interfejs API platformy Azure dla usługi FHIR przy użyciu szablonu ARM w Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż usługę Azure API for FHIR przy użyciu szablonu ARM w Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

Na stronie **wdrażanie interfejsu API platformy Azure dla FHIR** :

1. Jeśli chcesz, Zmień domyślną **subskrypcję** na inną subskrypcję.

2. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , wprowadź nazwę nowej grupy zasobów, a następnie wybierz pozycję **OK**.

3. Jeśli utworzono nową grupę zasobów, wybierz **region** dla grupy zasobów.

4. Wprowadź nową **nazwę usługi** i wybierz **lokalizację** interfejsu API platformy Azure dla usługi FHIR. Lokalizacja może być taka sama jak lub różna od regionu grupy zasobów.

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="Wdróż interfejs API platformy Azure dla usługi FHIR przy użyciu szablonu ARM w Azure Portal.":::

5. Wybierz pozycję **Przejrzyj i utwórz**.

6. Przeczytaj warunki i postanowienia, a następnie wybierz pozycję **Utwórz**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Jeśli chcesz uruchomić skrypty programu PowerShell lokalnie, najpierw wprowadź, `Connect-AzAccount` Aby skonfigurować swoje poświadczenia platformy Azure.

Jeśli `Microsoft.HealthcareApis` dostawca zasobów nie jest już zarejestrowany dla Twojej subskrypcji, możesz zarejestrować go przy użyciu następującego kodu interaktywnego. Aby uruchomić kod w Azure Cloud Shell, wybierz opcję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Użyj poniższego kodu, aby wdrożyć usługę Azure API for FHIR przy użyciu szablonu ARM. W kodzie zostanie wyświetlony komunikat z informacją o nowej nazwie usługi FHIR, nazwie nowej grupy zasobów i lokalizacji dla każdego z nich.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Jeśli `Microsoft.HealthcareApis` dostawca zasobów nie jest już zarejestrowany dla Twojej subskrypcji, możesz zarejestrować go przy użyciu następującego kodu interaktywnego. Aby uruchomić kod w Azure Cloud Shell, wybierz opcję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

```azurecli-interactive
az extension add --name healthcareapis
```

Użyj poniższego kodu, aby wdrożyć usługę Azure API for FHIR przy użyciu szablonu ARM. W kodzie zostanie wyświetlony komunikat z informacją o nowej nazwie usługi FHIR, nazwie nowej grupy zasobów i lokalizacji dla każdego z nich.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Wdrożenie może potrwać kilka minut. Zanotuj nazwy usługi Azure API for FHIR oraz grupę zasobów, której używasz do późniejszego przeglądania wdrożonych zasobów.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby zapoznać się z omówieniem nowego interfejsu API platformy Azure dla usługi FHIR:

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **Azure API for FHIR**.

2. Z listy FHIR wybierz nową usługę. Zostanie wyświetlona strona **Przegląd** nowej usługi Azure API for FHIR.

3. Aby sprawdzić, czy nowe konto interfejsu API FHIR jest inicjowane, wybierz link obok pozycji **punkt końcowy metadanych FHIR** , aby pobrać instrukcję obsługi interfejsu API FHIR. Link ma format `https://<service-name>.azurehealthcareapis.com/metadata` . Jeśli konto jest inicjowane, zostanie wyświetlony duży plik JSON.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje na temat usługi Azure API for FHIR. Musisz wprowadzić nazwę nowej usługi i grupy zasobów.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje na temat usługi Azure API for FHIR. Musisz wprowadzić nazwę nowej usługi i grupy zasobów.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
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

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu ARM, zapoznaj się z [samouczkiem dotyczącym tworzenia i wdrażania pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono interfejs API platformy Azure dla usługi FHIR w ramach subskrypcji. Aby ustawić dodatkowe ustawienia w interfejsie API platformy Azure dla usługi FHIR, należy przejoć do dodatkowych ustawień przewodnika. Jeśli wszystko jest gotowe do rozpoczęcia korzystania z interfejsu API platformy Azure dla usługi FHIR, Przeczytaj więcej na temat sposobu rejestrowania aplikacji.

>[!div class="nextstepaction"]
>[Dodatkowe ustawienia w interfejsie API platformy Azure dla usługi FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Przegląd rejestracji aplikacji](fhir-app-registration.md)
