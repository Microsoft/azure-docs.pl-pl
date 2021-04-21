---
title: Tworzenie funkcji języka C# z wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka C# z wiersza polecenia, a następnie opublikować projekt lokalny w hostingu bez serwera w Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
ms.openlocfilehash: e5a8a0a32196d4f4b988083f22930829418fa1b8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832018"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Szybki start: tworzenie funkcji języka C# na platformie Azure z wiersza polecenia

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

W tym artykule użyjemy narzędzi wiersza polecenia do utworzenia funkcji opartej na bibliotece klas języka C#, która odpowiada na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bez serwera Azure Functions.

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem konta platformy Azure w wysokości nie mniejszej niż kilka centów USD.

Istnieje również Visual Studio Code [tego artykułu.](create-first-function-vs-code-csharp.md)

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem musisz mieć następujące elementy:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Program [zestaw .NET Core SDK 3.1](https://www.microsoft.com/net/download)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#v2) 3.x.

+ Jedno z następujących narzędzi do tworzenia zasobów platformy Azure:

    + [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.4 lub nowszej.

    + [Azure PowerShell](/powershell/azure/install-az-ps) wersji 5.0 lub nowszej.

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Sprawdź wymagania wstępne, które zależą od tego, czy używasz interfejsu wiersza polecenia platformy Azure, Azure PowerShell do tworzenia zasobów platformy Azure:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

+ W terminalu lub oknie polecenia uruchom polecenie , aby sprawdzić, `func --version` czy Azure Functions Core Tools są w wersji 3.x.

+ Uruchom `az --version` narzędzie , aby sprawdzić, czy interfejs wiersza polecenia platformy Azure jest w wersji 2.4 lub nowszej.

+ Uruchom, `az login` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ Uruchom `dotnet --list-sdks` , aby sprawdzić, zestaw .NET Core SDK zainstalowano wersję 3.1.x

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ W terminalu lub oknie polecenia uruchom polecenie , aby sprawdzić, `func --version` czy Azure Functions Core Tools są w wersji 3.x.

+ Uruchom `(Get-Module -ListAvailable Az).Version` i zweryfikuj wersję 5.0 lub nowszą. 

+ Uruchom, `Connect-AzAccount` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ Uruchom `dotnet --list-sdks` , aby sprawdzić, zestaw .NET Core SDK zainstalowano wersję 3.1.x

---

## <a name="create-a-local-function-project"></a>Tworzenie lokalnego projektu funkcji

W Azure Functions projekt funkcji jest kontenerem dla co najmniej jednej funkcji, która odpowiada na określony wyzwalacz. Wszystkie funkcje w projekcie mają te same konfiguracje lokalne i konfiguracje hostingu. W tej sekcji utworzysz projekt funkcji, który zawiera jedną funkcję.

1. Uruchom polecenie w następujący sposób, aby utworzyć projekt funkcji `func init` w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. Przejdź do folderu projektu:

    ```console
    cd LocalFunctionProj
    ```

    Ten folder zawiera różne pliki projektu, w tym pliki konfiguracji o [nazwachlocal.settings.jsi](functions-run-local.md#local-settings-file) [host.jsna .](functions-host-json.md) Ponieważ *local.settings.jsmoże zawierać* wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *.gitignore.*

1. Dodaj funkcję do projektu przy użyciu następującego polecenia, gdzie argument jest unikatową nazwą funkcji (HttpExample), a argument określa wyzwalacz `--name` `--template` funkcji (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` Tworzy plik kodu HttpExample.cs.

### <a name="optional-examine-the-file-contents"></a>(Opcjonalnie) Badanie zawartości pliku

W razie potrzeby możesz od razu przejść do [tematu Uruchamianie funkcji lokalnie](#run-the-function-locally) i później przeanalizować zawartość pliku.

#### <a name="httpexamplecs"></a>HttpExample.cs

*Plik HttpExample.cs* zawiera metodę, która odbiera dane żądania w zmiennej , jest elementem HttpRequest, który jest dekorowany za pomocą wartości `Run` `req` **HttpTriggerAttribute,** [](/dotnet/api/microsoft.aspnetcore.http.httprequest) która definiuje zachowanie wyzwalacza.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Obiekt zwracany jest obiektem [ActionResult,](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) który zwraca komunikat odpowiedzi w następujący sposób: [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) lub [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Aby dowiedzieć się więcej, [zobacz Azure Functions wyzwalaczy i powiązań HTTP.](./functions-bindings-http-webhook.md?tabs=csharp)

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Utwórz aplikację funkcji na platformie Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Polecenie [az functionapp create](/cli/azure/functionapp#az_functionapp_create) tworzy aplikację funkcji na platformie Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Polecenie cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) tworzy aplikację funkcji na platformie Azure. 
    
    ---
    
    W poprzednim przykładzie zastąp nazwą konta użytego w poprzednim kroku, a zastąp wartością globalnie unikatową `<STORAGE_NAME>` `<APP_NAME>` nazwą odpowiednią dla Ciebie. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
    
    To polecenie tworzy aplikację funkcji uruchamianą w określonym środowisku uruchomieniowym języka w ramach [planu Azure Functions Consumption ,](consumption-plan.md)który jest bezpłatny dla ilości użycia, które zostanie naliczone w tym miejscu. Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitorowanie Azure Functions](functions-monitoring.md). Wystąpienie nie ponosi żadnych kosztów, dopóki nie zostanie aktywowane.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage]

[Nawiązywanie połączenia z kolejką usługi Azure Storage]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp
