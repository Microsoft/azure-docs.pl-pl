---
title: Szybki start — tworzenie i wdrażanie przepływu pracy aplikacji logiki przy użyciu Azure Resource Manager szablonów
description: Jak utworzyć i wdrożyć aplikację logiki przy użyciu Azure Resource Manager szablonów
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.date: 04/01/2021
ms.openlocfilehash: bdeb80dc487d8418e21a40a29e5d2baab49d7b05
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771897"
---
# <a name="quickstart-create-and-deploy-a-logic-app-workflow-by-using-an-arm-template"></a>Szybki start: tworzenie i wdrażanie przepływu pracy aplikacji logiki przy użyciu szablonu usługi ARM

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) to usługa w chmurze, która pomaga tworzyć i uruchamiać zautomatyzowane przepływy pracy, które integrują dane, aplikacje, usługi w chmurze i systemy lokalne, wybierając spośród setek [łączników.](/connectors/connector-reference/connector-reference-logicapps-connectors) Ten przewodnik Szybki start koncentruje się na procesie wdrażania szablonu usługi Azure Resource Manager (szablonu ARM) w celu utworzenia podstawowej aplikacji logiki, która sprawdza stan platformy Azure zgodnie z harmonogramem godzinowym. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto platformy [Azure.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Przegląd szablonu

W tym przewodniku Szybki start jest używany szablon Tworzenie aplikacji [**logiki,**](https://azure.microsoft.com/resources/templates/101-logic-app-create/) który można znaleźć w galerii szablonów szybkiego startu platformy [Azure,](https://azure.microsoft.com/resources/templates) ale jego wyświetlanie w tym miejscu trwa zbyt długo. Zamiast tego możesz przejrzeć szablon szybkiego startu ["azuredeploy.jsw pliku"](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) w galerii szablonów.

Szablon szybkiego startu tworzy przepływ pracy aplikacji logiki, który używa wyzwalacza Cyklicznie, który jest ustawiany do uruchamiania co godzinę, oraz wbudowanej akcji [  HTTP](../connectors/built-in.md), która wywołuje adres URL, który zwraca stan dla platformy Azure. Wbudowana akcja jest natywna dla Azure Logic Apps platformy.

Ten szablon tworzy następujący zasób platformy Azure:

* [**Microsoft.Logic/workflows**](/azure/templates/microsoft.logic/workflows), który tworzy przepływ pracy dla aplikacji logiki.

Aby znaleźć więcej szablonów szybkiego startu dla Azure Logic Apps, przejrzyj szablony [Microsoft.Logic](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Logic) w galerii.

<a name="deploy-template"></a>

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z opcją, której chcesz użyć do wdrożenia szablonu szybkiego startu:

| Opcja | Opis |
|--------|-------------|
| [Witryna Azure Portal](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-portal#deploy-template) | Jeśli środowisko platformy Azure spełnia wymagania wstępne i znasz szablony usługi ARM, te kroki ułatwiają logowanie się bezpośrednio do platformy Azure i otwieranie szablonu szybkiego startu w Azure Portal. Aby uzyskać więcej informacji, zobacz [Deploy resources with ARM templates and Azure Portal](../azure-resource-manager/templates/deploy-portal.md)(Wdrażanie zasobów za pomocą szablonów usługi ARM i Azure Portal ). |
| [Interfejs wiersza polecenia platformy Azure](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-cli#deploy-template) | Interfejs wiersza polecenia platformy Azure (Azure CLI) to zestaw poleceń do tworzenia zasobów platformy Azure i zarządzania nimi. Do uruchomienia tych poleceń potrzebny jest interfejs wiersza polecenia platformy Azure w wersji 2.6 lub nowszej. Aby sprawdzić wersję interfejsu wiersza polecenia, wpisz `az --version` . Więcej informacji można znaleźć w następujących tematach: <p><p>- [Co to jest interfejs wiersza polecenia platformy Azure](/cli/azure/what-is-azure-cli) <br>- [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) |
| [Azure PowerShell](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-powershell#deploy-template) | Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi Azure Resource Manager. Więcej informacji można znaleźć w następujących tematach: <p><p>- [Azure PowerShell omówienie](/powershell/azure/azurerm/overview) <br>- [Wprowadzenie do Azure PowerShell Az](/powershell/azure/new-azureps-module-az) <br>- [Wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps) |
| [Azure Resource Management REST API](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=rest-api#deploy-template) | Platforma Azure Representational State Transfer interfejsy API rest (REST), czyli punkty końcowe usługi, które obsługują operacje HTTP (metody), których używasz do tworzenia, pobierania, aktualizowania lub usuwania dostępu do zasobów usługi. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z interfejsem API REST platformy Azure.](/rest/api/azure/) |
|||

<a name="deploy-azure-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz poniższy obraz, aby zalogować się przy użyciu konta platformy Azure i otworzyć szablon szybkiego startu w Azure Portal:

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

1. W portalu na stronie **Tworzenie aplikacji logiki przy** użyciu szablonu wprowadź lub wybierz następujące wartości:

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Subskrypcja** | <*Nazwa subskrypcji platformy Azure*> | Nazwa subskrypcji platformy Azure do użycia |
   | **Grupa zasobów** | <*Nazwa grupy zasobów platformy Azure*> | Nazwa nowej lub istniejącej grupy zasobów platformy Azure. W tym przykładzie użyto wartości `Check-Azure-Status-RG`. |
   | **Region** | <*Region świadczenia usługi Azure*> | Region centrum danych platformy Azure do korzystania z aplikacji logiki. W tym przykładzie użyto wartości `West US`. |
   | **Nazwa aplikacji logiki** | <*logic-app-name*> | Nazwa do użycia dla aplikacji logiki. W tym przykładzie użyto wartości `Check-Azure-Status-LA`. |
   | **Testowy URI** | <*test-URI*> | URI dla usługi do wywołania na podstawie określonego harmonogramu. W tym przykładzie `https://status.azure.com/en-us/status/` użyto strony , która jest stroną stanu platformy Azure. |
   | **Lokalizacja** |  <*Region platformy Azure dla wszystkich zasobów*> | Region świadczenia usługi Azure do użycia dla wszystkich zasobów, jeśli różni się od wartości domyślnej. W tym przykładzie użyto wartości `[resourceGroup().location]` domyślnej , która jest lokalizacją grupy zasobów. |
   ||||

   Oto jak wygląda strona z wartościami użytymi w tym przykładzie:

   ![Podaj informacje dotyczące szablonu szybkiego startu](./media/quickstart-create-deploy-azure-resource-manager-template/create-logic-app-template-portal.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Przejrzyj i utwórz.**

1. Przejdź do kroków z tematu [Review deployed resources (Przeglądanie wdrożonych zasobów).](#review-deployed-resources)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name name to use for generating resource names:" projectName &&
read -p "Enter the location, such as 'westus':" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

Więcej informacji można znaleźć w następujących tematach:

* [Interfejs wiersza polecenia platformy Azure: az deployment group](/cli/azure/deployment/group)
* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name to use for generating resource names"
$location = Read-Host -Prompt "Enter the location, such as 'westus'"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

Więcej informacji można znaleźć w następujących tematach:

* [Azure PowerShell: New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
* [Azure PowerShell: New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

#### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

1. Jeśli nie chcesz używać istniejącej grupy zasobów platformy Azure, utwórz nową grupę zasobów, korzystając z następującej składni żądania, które wysyłasz do interfejsu API REST usługi Resource Management:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
   ```

   | Wartość | Opis |
   |-------|-------------|
   | `subscriptionId`| Identyfikator GUID subskrypcji platformy Azure, której chcesz użyć |
   | `resourceGroupName` | Nazwa grupy zasobów platformy Azure do utworzenia. W tym przykładzie użyto wartości `Check-Azure-Status-RG`. |
   |||

   Na przykład:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG?api-version=2019-10-01
   ```

   Więcej informacji można znaleźć w następujących tematach:

   * [Dokumentacja interfejsu API REST platformy Azure — jak wywołać interfejsy API REST platformy Azure](/rest/api/azure/)
   * [Interfejs API REST zarządzania zasobami: grupy zasobów — tworzenie lub aktualizowanie](/rest/api/resources/resourcegroups/createorupdate).

1. Aby wdrożyć szablon szybkiego startu w grupie zasobów, postępuj zgodnie z następującą składnią żądania, które wysyłasz do interfejsu API REST usługi Resource Management:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

   | Wartość | Opis |
   |-------|-------------|
   | `subscriptionId`| Identyfikator GUID subskrypcji platformy Azure, której chcesz użyć |
   | `resourceGroupName` | Nazwa grupy zasobów platformy Azure do użycia. W tym przykładzie użyto wartości `Check-Azure-Status-RG`. |
   | `deploymentName` | Nazwa do użycia dla wdrożenia. W tym przykładzie użyto wartości `Check-Azure-Status-LA`. |
   |||

   Na przykład:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG/providers/Microsoft.Resources/deployments/Check-Azure-Status-LA?api-version=2019-10-01
   ```

   Aby uzyskać więcej informacji, zobacz [Resource Management REST API: Deployments - Create Or Update (Interfejs API REST zarządzania zasobami: wdrożenia — tworzenie lub aktualizowanie).](/rest/api/resources/deployments/createorupdate)

1. Aby podać wartości do użycia dla wdrożenia, takie jak region platformy Azure i linki do szablonu szybkiego startu i pliku parametrów [,](../azure-resource-manager/templates/template-parameters.md)który zawiera wartości szablonu szybkiego startu do użycia podczas wdrażania, postępuj zgodnie z następującą składnią treści żądania, która jest wysyłana do interfejsu API REST usługi Resource Management:

   ```json
   {
      "location": "{Azure-region}",
      "properties": {
         "templateLink": {
            "uri": "{quickstart-template-URL}",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "{quickstart-template-parameter-file-URL}",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | `location`| <*Region świadczenia usługi Azure*> | Region świadczenia usługi Azure do użycia na użytek wdrożenia. W tym przykładzie użyto wartości `West US`. |
   | `templateLink` : `uri` | <*szybki start — adres URL szablonu*> | Lokalizacja adresu URL szablonu szybkiego startu do użycia podczas wdrażania: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json`. |
   | `parametersLink` : `uri` | <*quickstart-template-parameter-file-URL*> | Lokalizacja adresu URL pliku parametrów szablonu szybkiego startu do użycia podczas wdrażania: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json` <p><p>Aby uzyskać więcej informacji o Resource Manager parametrów, zobacz następujące tematy: <p><p>- [Tworzenie Resource Manager parametrów](../azure-resource-manager/templates/parameter-files.md) <br>- [Samouczek: wdrażanie szablonu usługi ARM przy użyciu plików parametrów](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md) |
   | `mode` | <*tryb wdrażania*> | Uruchom aktualizację przyrostową lub pełną. W tym przykładzie `Incremental` użyto wartości , która jest wartością domyślną. Aby uzyskać więcej informacji, [zobacz Azure Resource Manager wdrażania.](../azure-resource-manager/templates/deployment-modes.md) |
   |||

   Na przykład:

   ```json
   {
      "location": "West US",
      "properties": {
         "templateLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

Więcej informacji można znaleźć w następujących tematach:

* [Interfejs API REST zarządzania zasobami](/rest/api/resources/)
* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i Resource Manager API REST](../azure-resource-manager/templates/deploy-rest.md)

---

<a name="review-deployed-resources"></a>

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Aby wyświetlić aplikację logiki, możesz użyć interfejsu Azure Portal, uruchomić skrypt, który tworzysz za pomocą interfejsu wiersza polecenia platformy Azure lub interfejsu Azure PowerShell albo użyć interfejsu API REST aplikacji logiki.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W Azure Portal wyszukiwania wprowadź nazwę aplikacji logiki, która znajduje się `Check-Azure-Status-LA` w tym przykładzie. Z listy wyników wybierz aplikację logiki.

1. W Azure Portal znajdź i wybierz aplikację logiki, która znajduje się `Check-Azure-Status-RG` w tym przykładzie.

1. Po otworze Projektanta aplikacji logiki przejrzyj aplikację logiki utworzoną za pomocą szablonu szybkiego startu.

1. Aby przetestować aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli-interactive
echo "Enter your logic app name:" &&
read logicAppName &&
az logic workflow show --name $logicAppName &&
echo "Press [ENTER] to continue ..."
```

Aby uzyskać więcej informacji, zobacz [Interfejs wiersza polecenia platformy Azure: az logic workflow show](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show).

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$logicAppName = Read-Host -Prompt "Enter your logic app name"
Get-AzLogicApp -Name $logicAppName
Write-Host "Press [ENTER] to continue..."
```

Aby uzyskać więcej informacji, [zobacz Azure PowerShell: Get-AzLogicApp](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}?api-version=2016-06-01
```

| Wartość | Opis |
|-------|-------------|
| `subscriptionId`| Identyfikator GUID subskrypcji platformy Azure, w której wdrożono szablon szybkiego startu. |
| `resourceGroupName` | Nazwa grupy zasobów platformy Azure, w której wdrożono szablon szybkiego startu. W tym przykładzie użyto wartości `Check-Azure-Status-RG`. |
| `workflowName` | Nazwa wdrożonej aplikacji logiki. W tym przykładzie użyto wartości `Check-Azure-Status-LA`. |
|||

Na przykład:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG/providers/Microsoft.Logic/workflows/Check-Azure-Status-LA?api-version=2016-06-01
```

Aby uzyskać więcej informacji, zobacz [Logic Apps API REST: Workflows - Get](/rest/api/logic/workflows/get).

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start i samouczkami, warto zachować te zasoby. Gdy aplikacja logiki nie jest już potrzebna, usuń grupę zasobów przy użyciu interfejsu API REST usługi Azure Portal, interfejsu wiersza polecenia platformy Azure Azure PowerShell lub usługi Resource Management.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W Azure Portal znajdź i wybierz grupę zasobów, którą chcesz usunąć, która znajduje się `Check-Azure-Status-RG` w tym przykładzie.

1. W menu grupy zasobów wybierz pozycję **Przegląd,** jeśli nie została jeszcze wybrana. Na stronie przeglądu wybierz pozycję **Usuń grupę zasobów.**

1. Aby potwierdzić, wprowadź nazwę grupy zasobów.

Aby uzyskać więcej informacji, zobacz [Usuwanie grupy zasobów](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli-interactive
echo "Enter your resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

Aby uzyskać więcej informacji, zobacz Interfejs [wiersza polecenia platformy Azure: az group delete](/cli/azure/group#az_group_delete).

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Aby uzyskać więcej informacji, [zobacz Azure PowerShell: Remove-AzResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
```

| Wartość | Opis |
|-------|-------------|
| `subscriptionId`| Identyfikator GUID subskrypcji platformy Azure, w której wdrożono szablon szybkiego startu. |
| `resourceGroupName` | Nazwa grupy zasobów platformy Azure, w której wdrożono szablon szybkiego startu. W tym przykładzie użyto wartości `Check-Azure-Status-RG`. |
|||

Na przykład:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG?api-version=2019-10-01
```

Aby uzyskać więcej informacji, zobacz Resource Management REST API: Resource Groups - Delete (Interfejs [API REST zarządzania zasobami: grupy zasobów — usuwanie).](/rest/api/resources/resourcegroups/delete)

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: tworzenie i wdrażanie pierwszego szablonu usługi ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
