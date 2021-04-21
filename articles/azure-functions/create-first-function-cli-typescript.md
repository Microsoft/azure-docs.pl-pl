---
title: Tworzenie funkcji Języka TypeScript z wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję TypeScript z wiersza polecenia, a następnie opublikować projekt lokalny w hostingu bez serwera w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c7b37700d93ea129ba46de81989f06dd2afe52ba
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831820"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>Szybki start: tworzenie funkcji TypeScript na platformie Azure z wiersza polecenia

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

W tym artykule użyjemy narzędzi wiersza polecenia do utworzenia funkcji języka TypeScript, która odpowiada na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bez serwera Azure Functions.

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem konta platformy Azure o wartości kilku centów lub mniej.

Istnieje również wersja [Visual Studio Code tego](create-first-function-vs-code-typescript.md) artykułu oparta na języku.

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem musisz mieć następujące elementy:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Wersja [Azure Functions Core Tools](functions-run-local.md#v2) 3.x.

+ Jedno z następujących narzędzi do tworzenia zasobów platformy Azure:

    + [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.4 lub nowszej.

    + [Azure PowerShell](/powershell/azure/install-az-ps) w wersji 5.0 lub nowszej.

+ [Node.js](https://nodejs.org/), wersje Active LTS i Maintenance LTS (zalecane wersje 8.11.1 i 10.14.1).

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
    func init LocalFunctionProj --typescript
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

#### <a name="indexts"></a>index.ts

*Plik index.ts* eksportuje funkcję wyzwalaną zgodnie z konfiguracją w pliku *function.jsna .*

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

W przypadku wyzwalacza HTTP funkcja odbiera dane żądania w zmiennej typu `req` **HttpRequest** zgodnie z *definicją w* function.jsna . Zwracany obiekt, zdefiniowany w `$return`function.js *w obiekcie*, jest odpowiedzią. 

#### <a name="functionjson"></a>function.json

*function.jsjest* plikiem konfiguracji, który definiuje dane wejściowe i wyjściowe dla funkcji, w tym `bindings` typ wyzwalacza. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu i powiązanie [`httpTrigger`](functions-bindings-http-webhook-trigger.md) wyjściowe typu [`http`](functions-bindings-http-webhook-output.md) .

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

1. Uruchom funkcję przez uruchomienie lokalnego Azure Functions uruchomieniowego z folderu *LocalFunctionProj:*

    ```console
    npm install
    npm start
    ```
    
    Pod koniec danych wyjściowych powinny pojawić się następujące wiersze:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Jeśli przykład HttpExample nie jest wyświetlany, jak pokazano poniżej, host prawdopodobnie został uruchomiony spoza folderu głównego projektu. W takim przypadku użyj **klawisza Ctrl** C, aby zatrzymać hosta, przejść do folderu głównego projektu +  i ponownie uruchomić poprzednie polecenie.

1. Skopiuj adres URL funkcji z tych danych wyjściowych do przeglądarki i dołącz ciąg zapytania , dzięki czemu `HttpExample` `?name=<your-name>` pełny adres URL, taki jak `http://localhost:7071/api/HttpExample?name=Functions` . W przeglądarce powinien zostać wyświetlony komunikat, taki jak `Hello Functions` :

    ![Wynik działania funkcji lokalnie w przeglądarce](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Terminal, w którym uruchomiliśmy projekt, również wyświetla dane wyjściowe dziennika podczas żądań.

1. Gdy wszystko będzie gotowe, naciśnij **klawisze Ctrl** + **C** i wybierz `y` opcję zatrzymania hosta funkcji.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Utwórz aplikację funkcji na platformie Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Polecenie [az functionapp create](/cli/azure/functionapp#az_functionapp_create) tworzy aplikację funkcji na platformie Azure. Jeśli używasz programu Node.js 10, zmień również na `--runtime-version` `10` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Polecenie cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) tworzy aplikację funkcji na platformie Azure. Jeśli używasz aplikacji w Node.js 10, zmień `-RuntimeVersion` na `10` .

    ---
        
    W poprzednim przykładzie zastąp nazwą konta użytego w poprzednim kroku, a zastąp wartością globalnie unikatową `<STORAGE_NAME>` `<APP_NAME>` nazwą odpowiednią dla Ciebie. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
    
    To polecenie tworzy aplikację funkcji uruchamianą w środowisku uruchomieniowym określonego języka w ramach planu zużycie usługi [Azure Functions,](consumption-plan.md)który jest bezpłatny dla ilości użycia, które są tutaj naliczane. Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitorowanie Azure Functions](functions-monitoring.md). Wystąpienie nie ponosi żadnych kosztów, dopóki nie zostanie aktywowane.

## <a name="deploy-the-function-project-to-azure"></a>Wdrażanie projektu funkcji na platformie Azure

Przed użyciem narzędzi Core Tools do wdrożenia projektu na platformie Azure należy utworzyć gotową do produkcji kompilację plików JavaScript z plików źródłowych TypeScript.

1. Użyj następującego polecenia, aby przygotować projekt TypeScript do wdrożenia:

    ```console
    npm run build:production 
    ```

1. Po przygotowaniu niezbędnych zasobów możesz teraz wdrożyć lokalny projekt funkcji w aplikacji funkcji na platformie Azure przy użyciu polecenia [func azure functionapp publish.](functions-run-local.md#project-file-deployment) W poniższym przykładzie zastąp `<APP_NAME>` nazwą swojej aplikacji.

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    Jeśli zostanie wyświetlony błąd "Nie można znaleźć aplikacji o nazwie ...", zaczekaj kilka sekund i spróbuj ponownie, ponieważ platforma Azure może nie w pełni zainicjować aplikacji po poprzednim `az functionapp create` poleceniu.
    
    Polecenie publish wyświetla wyniki podobne do następujących danych wyjściowych (obciętych dla uproszczenia):
    
    <pre>
    ...
    
    Getting site publishing info...
    Creating archive for current directory...
    Performing remote build for functions project.
    
    ...
    
    Deployment successful.
    Remote build succeeded!
    Syncing triggers...
    Functions in msdocs-azurefunctions-qs:
        HttpExample - [httpTrigger]
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
