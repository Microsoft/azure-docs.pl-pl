---
title: Tworzenie pierwszej funkcji przy użyciu szablonów Azure Resource Manager
description: Tworzenie i wdrażanie na platformie Azure prostej wyzwalanej przez protokół HTTP funkcji bezserwerowej przy użyciu szablonu Azure Resource Manager (szablon ARM).
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 362ff5cd59982c1d848ed59af8381090344f5c5e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88642269"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Szybki Start: Tworzenie i wdrażanie zasobów Azure Functions przy użyciu szablonu ARM

W tym artykule opisano tworzenie funkcji reagującej na żądania HTTP przy użyciu szablonu Azure Resource Manager (szablonu ARM). 

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-account"></a>Konto platformy Azure 

Przed rozpoczęciem musisz mieć konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

### <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnych

Ten artykuł wymaga, aby projekt kodu funkcji lokalnych był uruchamiany w utworzonych zasobach platformy Azure. Jeśli nie utworzysz najpierw projektu do opublikowania, nie będzie można ukończyć sekcji wdrożenia tego artykułu. 

Wybierz jedną z następujących kart, Użyj linku i wypełnij sekcję, aby utworzyć aplikację funkcji w wybranym języku:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[Utwórz projekt funkcji lokalnych w Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project)

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[Tworzenie projektu funkcji lokalnych w programie Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

[Tworzenie projektu funkcji lokalnych z poziomu wiersza polecenia](functions-create-first-azure-function-azure-cli.md#create-a-local-function-project)

---

Po utworzeniu projektu lokalnie należy utworzyć zasoby wymagane do uruchomienia nowej funkcji na platformie Azure. 

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/).

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

Ten szablon tworzy następujące cztery zasoby platformy Azure:

+ [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): Utwórz konto usługi Azure Storage, które jest wymagane przez funkcje.
+ [**Microsoft. Web/dopuszczalna**](/azure/templates/microsoft.web/serverfarms): Utwórz plan hostingu bezserwerowego dla aplikacji funkcji.
+ [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): Utwórz aplikację funkcji.
+ [**Microsoft. Insights/Components**](/azure/templates/microsoft.insights/components): utwórz wystąpienie Application Insights do monitorowania.

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

Następnie sprawdź poprawność zasobów hostingu aplikacji funkcji utworzonych przez opublikowanie projektu na platformie Azure i wywołanie punktu końcowego HTTP funkcji.

### <a name="publish-the-function-project-to-azure"></a>Opublikuj projekt funkcji na platformie Azure

Aby opublikować projekt w nowych zasobach platformy Azure, wykonaj następujące kroki:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

W danych wyjściowych Skopiuj adres URL wyzwalacza HTTP. Służy do testowania funkcji działającej na platformie Azure. 

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W obszarze Wybieranie **elementu docelowego publikowania**wybierz pozycję **Azure Functions plan zużycia** , **wybierając pozycję istniejący** i wybierz pozycję **Utwórz profil**.

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Wybierz istniejący element docelowy publikowania":::

1. Wybierz swoją **subskrypcję**, rozwiń grupę zasobów, wybierz aplikację funkcji, a następnie wybierz **przycisk OK**.

1. Po zakończeniu publikowania Skopiuj **adres URL witryny**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="Wybierz istniejący element docelowy publikowania":::

1. Dołącz ścieżkę `/api/<FUNCTION_NAME>?name=Functions` , gdzie `<FUNCTION_NAME>` to nazwa funkcji. Adres URL, który wywołuje funkcję wyzwalacza HTTP, ma następujący format:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Ten adres URL jest używany do testowania funkcji wyzwalacza HTTP działającej na platformie Azure.

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Aby opublikować kod lokalny w aplikacji funkcji na platformie Azure, użyj `publish` polecenia:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

W tym przykładzie Zastąp `<FUNCTION_APP_NAME>` wartość nazwą aplikacji funkcji. Może być konieczne ponowne zalogowanie się za pomocą programu `az login` . 

W danych wyjściowych Skopiuj adres URL wyzwalacza HTTP. Służy do testowania funkcji działającej na platformie Azure.

---

### <a name="invoke-the-function-on-azure"></a>Wywoływanie funkcji na platformie Azure

Wklej adres URL skopiowany dla żądania HTTP na pasku adresu przeglądarki, upewnij się, że `name` ciąg zapytania został `?name=Functions` dołączony do końca tego adresu URL, a następnie wykonaj żądanie. 

Powinna zostać wyświetlona odpowiedź:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przejdziesz do następnego kroku i dodasz powiązanie danych wyjściowych kolejki usługi Azure Storage, Zachowaj wszystkie swoje zasoby w miejscu, w którym będziesz kompilować zawartość już wykonane.

W przeciwnym razie użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Zamień `<RESOURCE_GROUP_NAME>` na nazwę grupy zasobów.

## <a name="next-steps"></a>Następne kroki

Teraz, po opublikowaniu pierwszej funkcji, Dowiedz się więcej, dodając powiązanie danych wyjściowych do funkcji.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md)

---
