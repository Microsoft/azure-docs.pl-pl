---
title: Tworzenie funkcji programu PowerShell z wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję programu PowerShell z wiersza polecenia, a następnie opublikować lokalny projekt w hostingu bez serwera w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
- devx-track-azurepowershell
ms.openlocfilehash: 59532ce4dcc0c967777afd3080a2cb54dbaa6491
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831964"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>Szybki start: tworzenie funkcji programu PowerShell na platformie Azure z wiersza polecenia

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

W tym artykule użyjemy narzędzi wiersza polecenia do utworzenia funkcji programu PowerShell, która odpowiada na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bez serwera Azure Functions.

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem konta platformy Azure o wartości kilku centów lub mniej.

Istnieje również wersja [Visual Studio Code tego](create-first-function-vs-code-powershell.md) artykułu oparta na języku.

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem musisz mieć następujące elementy:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Wersja [Azure Functions Core Tools](functions-run-local.md#v2) 3.x.

+ Jedno z następujących narzędzi do tworzenia zasobów platformy Azure:

    + [Azure PowerShell](/powershell/azure/install-az-ps) w wersji 5.0 lub nowszej.

    + [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.4 lub nowszej.

+ Program [zestaw .NET Core SDK 3.1](https://www.microsoft.com/net/download)

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Sprawdź wymagania wstępne, które zależą od tego, czy używasz interfejsu wiersza polecenia platformy Azure, Azure PowerShell do tworzenia zasobów platformy Azure:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

+ W terminalu lub oknie polecenia uruchom polecenie , aby sprawdzić, `func --version` czy Azure Functions Core Tools są w wersji 3.x.

+ Uruchom `az --version` , aby sprawdzić, czy interfejs wiersza polecenia platformy Azure jest w wersji 2.4 lub nowszej.

+ Uruchom, `az login` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ W terminalu lub oknie polecenia uruchom polecenie , aby sprawdzić, `func --version` czy Azure Functions Core Tools są w wersji 3.x.

+ Uruchom `(Get-Module -ListAvailable Az).Version` i sprawdź wersję 5.0 lub nowszą. 

+ Uruchom, `Connect-AzAccount` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

---

## <a name="create-a-local-function-project"></a>Tworzenie lokalnego projektu funkcji

W Azure Functions projekt funkcji jest kontenerem dla co najmniej jednej funkcji, która odpowiada na określony wyzwalacz. Wszystkie funkcje w projekcie mają te same konfiguracje lokalne i konfiguracje hostingu. W tej sekcji utworzysz projekt funkcji zawierający jedną funkcję.

1. Uruchom następujące polecenie, aby utworzyć projekt funkcji w folderze o nazwie `func init` *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:  

    ```console
    func init LocalFunctionProj --powershell
    ```

1. Przejdź do folderu projektu:

    ```console
    cd LocalFunctionProj
    ```
    
    Ten folder zawiera różne pliki projektu, w tym pliki konfiguracji o [ nazwachlocal.settings.jsna](functions-run-local.md#local-settings-file) ihost.js[ na](functions-host-json.md). Ponieważ *local.settings.jsmoże* zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *gitignore.*
    
1. Dodaj funkcję do projektu przy użyciu następującego polecenia, gdzie argument jest unikatową nazwą funkcji (HttpExample), a argument określa wyzwalacz `--name` `--template` funkcji (HTTP). 

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new`Tworzy podfolder pasujący do nazwy funkcji, który zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o *nazwiefunction.jsna .*

### <a name="optional-examine-the-file-contents"></a>(Opcjonalnie) Badanie zawartości pliku

W razie potrzeby możesz przejść do [lokalnego](#run-the-function-locally) uruchomienia funkcji i później zbadać zawartość pliku.

#### <a name="runps1"></a>run.ps1

*run.ps1* definiuje skrypt funkcji, który jest wyzwalany zgodnie z konfiguracją w *programiefunction.jsna .*

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

W przypadku wyzwalacza HTTP funkcja odbiera dane żądania przekazane do param zdefiniowanego wfunction.js`$Request` *na .* Obiekt return, zdefiniowany wfunction.js, jest przekazywany do `Response` polecenia  `Push-OutputBinding` cmdlet jako odpowiedź. 

#### <a name="functionjson"></a>function.json

*function.jsjest* plikiem konfiguracji, który definiuje dane wejściowe i wyjściowe dla funkcji, w tym `bindings` typ wyzwalacza. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu i powiązanie [`httpTrigger`](functions-bindings-http-webhook-trigger.md) wyjściowe typu [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Utwórz aplikację funkcji na platformie Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Polecenie [az functionapp create](/cli/azure/functionapp#az_functionapp_create) tworzy aplikację funkcji na platformie Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Polecenie cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) tworzy aplikację funkcji na platformie Azure. 
    
    ---
    
    W poprzednim przykładzie zastąp nazwą konta użytego w poprzednim kroku, a zastąp wartością globalnie unikatową `<STORAGE_NAME>` `<APP_NAME>` nazwą odpowiednią dla Ciebie. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
    
    To polecenie tworzy aplikację funkcji uruchamianą w środowisku uruchomieniowym określonego języka w ramach planu zużycie usługi [Azure Functions,](consumption-plan.md)który jest bezpłatny dla ilości użycia, które są tutaj naliczane. Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie poniesie żadnych kosztów, dopóki nie zostanie aktywowane.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Łączenie z kolejką usługi Azure Storage]

[Łączenie z kolejką usługi Azure Storage]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
