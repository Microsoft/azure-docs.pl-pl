---
title: Tworzenie funkcji języka TypeScript przy użyciu wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka TypeScript z poziomu wiersza polecenia, a następnie opublikować projekt lokalny na potrzeby hostingu bezserwerowego w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 488ef9fa3fd5b6c09ed435483dbf8f6fa3eb5bef
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937199"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>Szybki Start: Tworzenie funkcji języka TypeScript na platformie Azure z poziomu wiersza polecenia

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji języka TypeScript, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

Istnieje również oparta na [Visual Studio Code wersja](create-first-function-vs-code-typescript.md) tego artykułu.

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy wykonać następujące czynności:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) w wersji 3. x.

+ Jedno z następujących narzędzi do tworzenia zasobów platformy Azure:

    + [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2,4 lub nowszej.

    + [Azure PowerShell](/powershell/azure/install-az-ps) w wersji 5,0 lub nowszej.

+ [Node.js](https://nodejs.org/), aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Sprawdź wymagania wstępne, które zależą od tego, czy używasz interfejsu wiersza polecenia platformy Azure, czy Azure PowerShell do tworzenia zasobów platformy Azure:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

+ W terminalu lub oknie poleceń Uruchom polecenie, `func --version` Aby sprawdzić, czy Azure Functions Core Tools są w wersji 3. x.

+ Uruchom, `az --version` Aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure to 2,4 lub nowszego.

+ Uruchom, `az login` Aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ W terminalu lub oknie poleceń Uruchom polecenie, `func --version` Aby sprawdzić, czy Azure Functions Core Tools są w wersji 3. x.

+ Uruchom `(Get-Module -ListAvailable Az).Version` i sprawdź wersję 5,0 lub nowszą. 

+ Uruchom, `Connect-AzAccount` Aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

---

## <a name="create-a-local-function-project"></a>Utwórz projekt funkcji lokalnej

W Azure Functions, projekt funkcji jest kontenerem dla jednej lub kilku poszczególnych funkcji, które reagują na konkretny wyzwalacz. Wszystkie funkcje w projekcie mają takie same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji zawierający pojedynczą funkcję.

1. Uruchom `func init` polecenie w następujący sposób, aby utworzyć projekt Functions w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:  

    ```console
    func init LocalFunctionProj --typescript
    ```

1. Przejdź do folderu projektu:

    ```console
    cd LocalFunctionProj
    ```
    
    Ten folder zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [local.settings.js](functions-run-local.md#local-settings-file) i [host.jsna](functions-host-json.md). Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *. gitignore* .

1. Dodaj funkcję do projektu za pomocą następującego polecenia, gdzie `--name` argument jest unikatową nazwą funkcji (HttpExample), a `--template` argument określa wyzwalacz funkcji (http).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    
    `func new` tworzy podfolder pasujący do nazwy funkcji, która zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o nazwie *function.json*.

### <a name="optional-examine-the-file-contents"></a>Obowiązkowe Sprawdzanie zawartości pliku

W razie potrzeby możesz pominąć, aby [uruchomić funkcję lokalnie](#run-the-function-locally) i później przejrzeć zawartość pliku.

#### <a name="indexts"></a>indeks. TS

*index. TS* eksportuje funkcję, która jest wyzwalana zgodnie z konfiguracją w *function.jsna*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Dla wyzwalacza HTTP funkcja otrzymuje dane żądania w zmiennej `req` typu **HttpRequest** zgodnie z definicją w *function.json*. Obiekt zwracany, zdefiniowany jako `$return` w *function.json*, jest odpowiedzią. 

#### <a name="functionjson"></a>function.json

*function.json* to plik konfiguracji, który definiuje dane wejściowe i wyjściowe `bindings` dla funkcji, w tym typ wyzwalacza. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu [`httpTrigger`](functions-bindings-http-webhook-trigger.md) i powiązania danych wyjściowych typu [`http`](functions-bindings-http-webhook-output.md) .

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

1. Uruchom funkcję przez uruchomienie lokalnego hosta Azure Functions środowiska uruchomieniowego z folderu *LocalFunctionProj* :

    ```console
    npm install
    npm start
    ```
    
    W kierunku końca danych wyjściowych powinny być wyświetlane następujące wiersze:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Jeśli HttpExample nie pojawia się, jak pokazano poniżej, prawdopodobnie uruchomiono hosta spoza folderu głównego projektu. W takim przypadku użyj **klawiszy CTRL** + **C** , aby zatrzymać hosta, przejdź do folderu głównego projektu i ponownie uruchom poprzednie polecenie.

1. Skopiuj adres URL `HttpExample` funkcji z tego danych wyjściowych do przeglądarki i dołącz ciąg zapytania `?name=<your-name>` , wprowadzając pełny adres URL, taki jak `http://localhost:7071/api/HttpExample?name=Functions` . W przeglądarce powinien zostać wyświetlony następujący komunikat `Hello Functions` :

    ![Wynik funkcji uruchomionej lokalnie w przeglądarce](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Na terminalu, w którym rozpoczął się projekt, są również wyświetlane dane wyjściowe dziennika podczas zgłaszania żądań.

1. Gdy wszystko będzie gotowe, użyj **klawiszy CTRL** + **C** i wybierz opcję `y` zatrzymania hosta funkcji.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Tworzenie aplikacji funkcji na platformie Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Polecenie [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) tworzy aplikację funkcji na platformie Azure. Jeśli używasz Node.js 10, Zmień również `--runtime-version` na `10` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Polecenie cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) tworzy aplikację funkcji na platformie Azure. Jeśli używasz Node.js 10, przejdź `-RuntimeVersion` do `10` .

    ---
        
    W poprzednim przykładzie Zastąp ciąg `<STORAGE_NAME>` nazwą konta użytego w poprzednim kroku i Zastąp ciąg `<APP_NAME>` globalnie unikatową nazwą, która jest odpowiednia dla Ciebie. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
    
    To polecenie tworzy aplikację funkcji działającą w określonym środowisku uruchomieniowym języka zgodnie z [planem zużycia Azure Functions](consumption-plan.md), który jest bezpłatny dla nakładu pracy w tym miejscu. Polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą którego można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie wiąże się z żadnymi kosztami, dopóki nie zostanie uaktywnione.

## <a name="deploy-the-function-project-to-azure"></a>Wdróż projekt funkcji na platformie Azure

Przed rozpoczęciem wdrażania projektu na platformie Azure za pomocą podstawowych narzędzi można utworzyć kompilację plików JavaScript w środowisku produkcyjnym na podstawie plików źródłowych TypeScript.

1. Użyj następującego polecenia, aby przygotować projekt TypeScript do wdrożenia:

    ```console
    npm run build:production 
    ```

1. W przypadku niezbędnych zasobów możesz teraz przystąpić do wdrażania projektu funkcji lokalnych w aplikacji funkcji na platformie Azure przy użyciu polecenia [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . W poniższym przykładzie Zastąp `<APP_NAME>` nazwą swojej aplikacji.

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    Jeśli zobaczysz błąd, "nie można odnaleźć aplikacji o nazwie...", poczekaj kilka sekund i spróbuj ponownie, ponieważ platforma Azure mogła nie zostać w pełni zainicjowana przez poprzednie `az functionapp create` polecenie.
    
    Polecenie Publikuj wyświetla wyniki podobne do następujących danych wyjściowych (obcięty dla uproszczenia):
    
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
