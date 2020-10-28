---
title: Szybki Start — tworzenie i wdrażanie przepływu pracy aplikacji logiki przy użyciu szablonów Azure Resource Manager
description: Jak utworzyć i wdrożyć aplikację logiki przy użyciu szablonów Azure Resource Manager
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.date: 06/30/2020
ms.openlocfilehash: 689582d73e29cb60cc2ee5294b568b5db2f73244
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739341"
---
# <a name="quickstart-create-and-deploy-a-logic-app-workflow-by-using-an-arm-template"></a>Szybki Start: Tworzenie i wdrażanie przepływu pracy aplikacji logiki przy użyciu szablonu ARM

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) to usługa w chmurze, która ułatwia tworzenie i uruchamianie zautomatyzowanych przepływów pracy, które integrują dane, aplikacje, usługi w chmurze i systemy lokalne poprzez wybranie [setek łączników](/connectors/connector-reference/connector-reference-logicapps-connectors). Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Azure Resource Manager (szablon ARM) w celu utworzenia podstawowej aplikacji logiki, która sprawdza stan platformy Azure zgodnie z harmonogramem godzinowym. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="review-the-template"></a>Przegląd szablonu

W tym przewodniku Szybki Start jest używany szablon [**Tworzenie aplikacji logiki**](https://azure.microsoft.com/resources/templates/101-logic-app-create/) , który można znaleźć w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates) , ale jest on zbyt długi, aby go wyświetlić. Zamiast tego można zapoznać się z szablonem szybkiego startu ["azuredeploy.jsw pliku"](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) w galerii szablonów.

Szablon szybkiego startu tworzy przepływ pracy aplikacji logiki, który używa wyzwalacza cyklu, który jest ustawiany do uruchamiania co godzinę, oraz [akcji *wbudowanej*](../connectors/apis-list.md#connector-types)protokołu HTTP, która wywołuje adres URL, który zwraca stan platformy Azure. Wbudowana akcja jest natywna dla platformy Azure Logic Apps.

Ten szablon tworzy następujący zasób platformy Azure:

* [**Microsoft. logiki/przepływy pracy**](/azure/templates/microsoft.logic/workflows), które tworzą przepływ pracy dla aplikacji logiki.

Aby znaleźć więcej szablonów szybkiego startu dla Azure Logic Apps, zapoznaj się z szablonami [Microsoft. logiki](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Logic) w galerii.

<a name="deploy-template"></a>

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Postępuj zgodnie z opcją, która ma zostać użyta do wdrożenia szablonu szybkiego startu:

| Opcja | Opis |
|--------|-------------|
| [Witryna Azure Portal](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-portal#deploy-template) | Jeśli środowisko platformy Azure spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, te kroki ułatwiają logowanie się bezpośrednio do platformy Azure i otwieranie szablonu szybkiego startu w Azure Portal. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure Portal](../azure-resource-manager/templates/deploy-portal.md). |
| [Interfejs wiersza polecenia platformy Azure](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-cli#deploy-template) | Interfejs wiersza polecenia platformy Azure (Azure CLI) to zestaw poleceń umożliwiających tworzenie zasobów platformy Azure i zarządzanie nimi. Aby uruchomić te polecenia, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2,6 lub nowszej. Aby sprawdzić wersję interfejsu wiersza polecenia, wpisz `az --version` . Więcej informacji można znaleźć w następujących tematach: <p><p>- [Co to jest interfejs wiersza polecenia platformy Azure](/cli/azure/what-is-azure-cli?view=azure-cli-latest) <br>- [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) |
| [Azure PowerShell](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-powershell#deploy-template) | Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi Azure Resource Manager. Więcej informacji można znaleźć w następujących tematach: <p><p>- [Przegląd Azure PowerShell](/powershell/azure/azurerm/overview) <br>- [Wprowadzenie Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az) <br>- [Wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps) |
| [Interfejs API REST usługi Azure Resource Management](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=rest-api#deploy-template) | Platforma Azure udostępnia interfejsy API REST (Representational State Transfer), które są punktami końcowymi usługi, które obsługują operacje HTTP (metody) używane do tworzenia, pobierania, aktualizowania lub usuwania dostępu do zasobów usługi. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z interfejsem API REST platformy Azure](/rest/api/azure/). |
|||

<a name="deploy-azure-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz następujący obraz, aby zalogować się przy użyciu konta platformy Azure i otworzyć szablon szybkiego startu w Azure Portal:

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

1. W portalu na stronie **Tworzenie aplikacji logiki przy użyciu szablonu** wprowadź lub wybierz następujące wartości:

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Subskrypcja** | <*Azure — nazwa subskrypcji*> | Nazwa subskrypcji platformy Azure do użycia |
   | **Grupa zasobów** | <*Azure-Resource-Group-Name*> | Nazwa nowej lub istniejącej grupy zasobów platformy Azure. Ten przykład używa `Check-Azure-Status-RG` . |
   | **Region** | <*Platforma Azure — region*> | Region centrum danych platformy Azure do korzystania z aplikacji logiki. Ten przykład używa `West US` . |
   | **Nazwa aplikacji logiki** | <*Logic-App-Name*> | Nazwa, która ma być używana w aplikacji logiki. Ten przykład używa `Check-Azure-Status-LA` . |
   | **Identyfikator URI testu** | <*Identyfikator URI testu*> | Identyfikator URI usługi do wywołania na podstawie określonego harmonogramu. Ten przykład używa `https://status.azure.com/en-us/status/` , który jest stroną stanu platformy Azure. |
   | **Lokalizacja** |  <*Azure-region-wszystkie zasoby*> | Region świadczenia usługi Azure, który ma być używany dla wszystkich zasobów, jeśli jest różny od wartości domyślnej. W tym przykładzie jest użyta wartość domyślna, `[resourceGroup().location]` która jest lokalizacją grupy zasobów. |
   ||||

   Oto, jak strona będzie wyglądać z wartościami użytymi w tym przykładzie:

   ![Podaj informacje o szablonie szybkiego startu](./media/quickstart-create-deploy-azure-resource-manager-template/create-logic-app-template-portal.png)

1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz** .

1. Wykonaj kroki opisane w sekcji [Przegląd wdrożonych zasobów](#review-deployed-resources).

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

* [Interfejs wiersza polecenia platformy Azure: AZ Deployment Group](/cli/azure/deployment/group)
* [Wdrażanie zasobów za pomocą szablonów ARM i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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
* [Wdrażanie zasobów za pomocą szablonów ARM i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

#### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

1. Jeśli nie chcesz używać istniejącej grupy zasobów platformy Azure, Utwórz nową grupę zasobów, wykonując następującą składnię dla żądania wysyłanego do interfejsu API REST zarządzania zasobami:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
   ```

   | Wartość | Opis |
   |-------|-------------|
   | `subscriptionId`| Identyfikator GUID subskrypcji platformy Azure, której chcesz użyć |
   | `resourceGroupName` | Nazwa grupy zasobów platformy Azure do utworzenia. Ten przykład używa `Check-Azure-Status-RG` . |
   |||

   Na przykład:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG?api-version=2019-10-01
   ```

   Więcej informacji można znaleźć w następujących tematach:

   * [Dokumentacja interfejsu API REST platformy Azure — jak wywoływać interfejsy API REST platformy Azure](/rest/api/azure/)
   * [Interfejs API REST zarządzania zasobami: grupy zasobów — Tworzenie lub aktualizowanie](/rest/api/resources/resourcegroups/createorupdate).

1. Aby wdrożyć szablon szybkiego startu w grupie zasobów, należy wykonać następującą składnię dla żądania wysyłanego do interfejsu API REST zarządzania zasobami:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

   | Wartość | Opis |
   |-------|-------------|
   | `subscriptionId`| Identyfikator GUID subskrypcji platformy Azure, której chcesz użyć |
   | `resourceGroupName` | Nazwa grupy zasobów platformy Azure, która ma zostać użyta. Ten przykład używa `Check-Azure-Status-RG` . |
   | `deploymentName` | Nazwa do użycia we wdrożeniu. Ten przykład używa `Check-Azure-Status-LA` . |
   |||

   Na przykład:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG/providers/Microsoft.Resources/deployments/Check-Azure-Status-LA?api-version=2019-10-01
   ```

   Aby uzyskać więcej informacji, zobacz [API REST zarządzania zasobami: Deployments-Create lub Update](/rest/api/resources/deployments/createorupdate).

1. Aby podać wartości używane do wdrożenia, takie jak region platformy Azure i linki do szablonu szybkiego startu i [pliku parametrów](../azure-resource-manager/templates/template-parameters.md), który zawiera wartości szablonu szybkiego startu do użycia we wdrożeniu, postępuj zgodnie z następującą składnią dla treści żądania wysyłanej do interfejsu API REST zarządzania zasobami:

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
   | `location`| <*Platforma Azure — region*> | Region świadczenia usługi Azure, który ma zostać użyty do wdrożenia. Ten przykład używa `West US` . |
   | `templateLink` : `uri` | <*Szybki Start — szablon — adres URL*> | Lokalizacja adresu URL szablonu szybkiego startu do użycia na potrzeby wdrożenia: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json`. |
   | `parametersLink` : `uri` | <*Szybki Start — szablon-parametr-File-URL*> | Lokalizacja adresu URL dla pliku parametru szablonu szybkiego startu, który ma zostać użyty do wdrożenia: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json` <p><p>Aby uzyskać więcej informacji na temat pliku parametrów Menedżer zasobów, zobacz następujące tematy: <p><p>- [Utwórz plik parametrów Menedżer zasobów](../azure-resource-manager/templates/parameter-files.md) <br>- [Samouczek: Wdrażanie szablonu ARM przy użyciu plików parametrów](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md) |
   | `mode` | <*Tryb wdrożenia*> | Uruchom aktualizację przyrostową lub pełną aktualizację. W tym przykładzie `Incremental` zostanie użyta wartość domyślna. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager trybami wdrożenia](../azure-resource-manager/templates/deployment-modes.md). |
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
* [Wdrażanie zasobów za pomocą szablonów ARM i interfejsu API REST Menedżer zasobów](../azure-resource-manager/templates/deploy-rest.md)

---

<a name="review-deployed-resources"></a>

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Aby wyświetlić aplikację logiki, można użyć Azure Portal, uruchomić skrypt utworzony za pomocą interfejsu wiersza polecenia platformy Azure lub Azure PowerShell lub użyć interfejsu API REST usługi Logic App.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W polu wyszukiwania Azure Portal wprowadź nazwę aplikacji logiki, która jest `Check-Azure-Status-LA` w tym przykładzie. Z listy wyników wybierz aplikację logiki.

1. W Azure Portal Znajdź i wybierz aplikację logiki, która jest `Check-Azure-Status-RG` w tym przykładzie.

1. Gdy zostanie otwarty projektant aplikacji logiki, przejrzyj aplikację logiki utworzoną przez szablon szybkiego startu.

1. Aby przetestować aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom** .

### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli-interactive
echo "Enter your logic app name:" &&
read logicAppName &&
az logic workflow show --name $logicAppName &&
echo "Press [ENTER] to continue ..."
```

Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure: AZ Logic Workflow show](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-show).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$logicAppName = Read-Host -Prompt "Enter your logic app name"
Get-AzLogicApp -Name $logicAppName
Write-Host "Press [ENTER] to continue..."
```

Aby uzyskać więcej informacji, zobacz [Azure PowerShell: Get-AzLogicApp](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}?api-version=2016-06-01
```

| Wartość | Opis |
|-------|-------------|
| `subscriptionId`| Identyfikator GUID subskrypcji platformy Azure, w której został wdrożony szablon szybkiego startu. |
| `resourceGroupName` | Nazwa grupy zasobów platformy Azure, w której wdrożono szablon szybkiego startu. Ten przykład używa `Check-Azure-Status-RG` . |
| `workflowName` | Nazwa wdrożonej aplikacji logiki. Ten przykład używa `Check-Azure-Status-LA` . |
|||

Na przykład:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG/providers/Microsoft.Logic/workflows/Check-Azure-Status-LA?api-version=2016-06-01
```

Aby uzyskać więcej informacji, zobacz [Logic Apps interfejsu API REST: przepływy pracy — Pobierz](/rest/api/logic/workflows/get).

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, możesz chcieć zachować te zasoby. Gdy aplikacja logiki nie jest już potrzebna, Usuń grupę zasobów przy użyciu interfejsu API REST usługi Azure Portal, Azure PowerShell lub zarządzania zasobami.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W Azure Portal Znajdź i wybierz grupę zasobów, którą chcesz usunąć, która jest `Check-Azure-Status-RG` w tym przykładzie.

1. W menu Grupa zasobów wybierz pozycję **Przegląd** , jeśli nie została jeszcze wybrana. Na stronie Przegląd wybierz pozycję **Usuń grupę zasobów** .

1. Aby potwierdzić, wprowadź nazwę grupy zasobów.

Aby uzyskać więcej informacji, zobacz [Usuwanie grupy zasobów](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli-interactive
echo "Enter your resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure: AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az-group-delete).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Aby uzyskać więcej informacji, zobacz [Azure PowerShell: Remove-AzResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
```

| Wartość | Opis |
|-------|-------------|
| `subscriptionId`| Identyfikator GUID subskrypcji platformy Azure, w której został wdrożony szablon szybkiego startu. |
| `resourceGroupName` | Nazwa grupy zasobów platformy Azure, w której wdrożono szablon szybkiego startu. Ten przykład używa `Check-Azure-Status-RG` . |
|||

Na przykład:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG?api-version=2019-10-01
```

Aby uzyskać więcej informacji, zobacz [interfejs API REST zarządzania zasobami: grupy zasobów-Usuń](/rest/api/resources/resourcegroups/delete).

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
