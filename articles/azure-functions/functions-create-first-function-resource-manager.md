---
title: Tworzenie pierwszej funkcji przy użyciu Azure Resource Manager szablonów
description: Tworzenie i wdrażanie na platformie Azure prostej funkcji bez serwera wyzwalanej przez protokół HTTP przy użyciu szablonu Azure Resource Manager (szablonu ARM).
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 0badc233597c13228e9826ed062cc15828c65cf2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833386"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Szybki start: tworzenie i wdrażanie Azure Functions zasobów z szablonu usługi ARM

W tym artykule użyjemy szablonu usługi Azure Resource Manager arm w celu utworzenia funkcji, która odpowiada na żądania HTTP. 

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem konta platformy Azure w wysokości nie mniejszej niż kilka centów USD. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-account"></a>Konto platformy Azure 

Przed rozpoczęciem musisz mieć konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/)

### <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnych

Ten artykuł wymaga uruchomienia lokalnego projektu kodu funkcji na tworzyć zasoby platformy Azure. Jeśli nie utworzysz najpierw projektu do opublikowania, nie będzie można ukończyć sekcji wdrażania tego artykułu. 

Wybierz jedną z następujących kart, kliknij link i ukończ sekcję, aby utworzyć aplikację funkcji w języku, który chcesz wybrać:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Utwórz projekt funkcji lokalnych w wybranym języku w Visual Studio Code:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [Program PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Tworzenie projektu funkcji lokalnych w programie Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Utwórz projekt funkcji lokalnych w wybranym języku z wiersza polecenia:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [Program PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

Po utworzeniu projektu lokalnie należy utworzyć zasoby wymagane do uruchomienia nowej funkcji na platformie Azure. 

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/).

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

Ten szablon tworzy cztery następujące zasoby platformy Azure:

+ [**Microsoft.Storage/storageAccounts:**](/azure/templates/microsoft.storage/storageaccounts)utwórz konto usługi Azure Storage, które jest wymagane przez usługę Functions.
+ [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)utwórz bez użycia serwera plan hostingu dla aplikacji funkcji.
+ [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)utwórz aplikację funkcji.
+ [**microsoft.insights/components:**](/azure/templates/microsoft.insights/components)utwórz Application Insights do monitorowania.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Następnie zweryfikuj utworzoną aplikację funkcji hostowania zasobów, publikując projekt na platformie Azure i wywołując punkt końcowy HTTP funkcji.

### <a name="publish-the-function-project-to-azure"></a>Publikowanie projektu funkcji na platformie Azure

Aby opublikować projekt w nowych zasobach platformy Azure, należy wykonać następujące kroki:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

W danych wyjściowych skopiuj adres URL wyzwalacza HTTP. Umożliwia to przetestowanie funkcji uruchomionej na platformie Azure. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. Na **stronie Wybierz miejsce docelowe publikacji** wybierz pozycję Azure Functions **Zużycie** z **wybierz** istniejący i wybierz pozycję **Utwórz profil.**

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Wybieranie istniejącego obiektu docelowego publikowania":::

1. Wybierz **subskrypcję**, rozwiń grupę zasobów, wybierz aplikację funkcji i wybierz przycisk **OK.**

1. Po zakończeniu publikowania skopiuj adres **URL witryny**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="Kopiowanie adresu URL witryny z podsumowania publikowania":::

1. Dołącz `/api/<FUNCTION_NAME>?name=Functions` ścieżkę , `<FUNCTION_NAME>` gdzie jest nazwą funkcji. Adres URL wywołujący funkcję wyzwalacza HTTP ma następujący format:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Ten adres URL umożliwia przetestowanie funkcji wyzwalacza HTTP uruchomionej na platformie Azure.

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Aby opublikować kod lokalny w aplikacji funkcji na platformie Azure, użyj `publish` polecenia :

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

W tym przykładzie `<FUNCTION_APP_NAME>` zastąp nazwą swojej aplikacji funkcji. Może być konieczne zalogowanie się ponownie przy `az login` użyciu . 

W danych wyjściowych skopiuj adres URL wyzwalacza HTTP. Umożliwia to przetestowanie funkcji uruchomionej na platformie Azure.

---

### <a name="invoke-the-function-on-azure"></a>Wywoływanie funkcji na platformie Azure

Wklej adres URL skopiowany dla żądania HTTP na pasku adresu przeglądarki, upewnij się, że ciąg zapytania został dołączony na końcu tego adresu URL, a następnie `name` `?name=Functions` wykonaj żądanie. 

Powinna zostać wyświetlony odpowiedź, na przykład:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli dodasz powiązanie wyjściowe kolejki usługi Azure Storage do następnego kroku, zachowaj wszystkie zasoby na miejscu, ponieważ będziesz tworzyć na podstawie już wykonanej pracy.

W przeciwnym razie użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć dodatkowych kosztów.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Zastąp `<RESOURCE_GROUP_NAME>` nazwą grupy zasobów.

## <a name="next-steps"></a>Następne kroki

Teraz, po opublikowaniu pierwszej funkcji, dowiedz się więcej, dodając powiązanie wyjściowe do funkcji.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md)

---
