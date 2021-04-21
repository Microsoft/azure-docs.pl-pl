---
title: Tworzenie funkcji języka Python z wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Python z wiersza polecenia, a następnie opublikować lokalny projekt w hostingu bez serwera w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-python-uiex
ms.openlocfilehash: f5c51630d111bd68e311a93100abb8266e2a8e27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787435"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Szybki start: tworzenie funkcji języka Python na platformie Azure z wiersza polecenia

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

W tym artykule użyjemy narzędzi wiersza polecenia, aby utworzyć funkcję języka Python, która odpowiada na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bez serwera Azure Functions.

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem konta platformy Azure w wysokości nie mniejszej niż kilka centów USD.

Istnieje również Visual Studio Code [tego artykułu.](create-first-function-vs-code-python.md)

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem musisz mieć następujące elementy:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#v2) 3.x.
  
+ Jedno z następujących narzędzi do tworzenia zasobów platformy Azure:

    + [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.4 lub nowszej.

    + [Azure PowerShell](/powershell/azure/install-az-ps) wersji 5.0 lub nowszej.

+ [Wersje języka Python obsługiwane przez Azure Functions](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Sprawdź wymagania wstępne, które zależą od tego, czy używasz interfejsu wiersza polecenia platformy Azure, Azure PowerShell do tworzenia zasobów platformy Azure:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

+ W terminalu lub oknie polecenia uruchom polecenie , aby sprawdzić, `func --version` czy Azure Functions Core Tools są w wersji 3.x.

+ Uruchom `az --version` narzędzie , aby sprawdzić, czy interfejs wiersza polecenia platformy Azure jest w wersji 2.4 lub nowszej.

+ Uruchom, `az login` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ Uruchom system (Linux/macOS) lub (Windows), aby sprawdzić raporty dotyczące wersji języka Python w wersji `python --version` `py --version` 3.8.x, 3.7.x lub 3.6.x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ W terminalu lub oknie polecenia uruchom polecenie , aby sprawdzić, `func --version` czy Azure Functions Core Tools są w wersji 3.x.

+ Uruchom `(Get-Module -ListAvailable Az).Version` i zweryfikuj wersję 5.0 lub nowszą. 

+ Uruchom, `Connect-AzAccount` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ Uruchom system (Linux/macOS) lub (Windows), aby sprawdzić raporty dotyczące wersji języka Python w wersji `python --version` `py --version` 3.8.x, 3.7.x lub 3.6.x.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Tworzenie i aktywowanie środowiska wirtualnego

W odpowiednim folderze uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv` . Pamiętaj, aby używać języka Python w wersji 3.8, 3.7 lub 3.6, które są obsługiwane przez Azure Functions.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Jeśli język Python nie zainstaluje pakietu venv w twojej dystrybucji systemu Linux, uruchom następujące polecenie:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Wszystkie kolejne polecenia są uruchamiane w tym aktywowanym środowisku wirtualnym. 

## <a name="create-a-local-function-project"></a>Tworzenie lokalnego projektu funkcji

W Azure Functions projekt funkcji jest kontenerem dla co najmniej jednej funkcji, która odpowiada na określony wyzwalacz. Wszystkie funkcje w projekcie mają te same konfiguracje lokalne i konfiguracje hostingu. W tej sekcji utworzysz projekt funkcji, który zawiera jedną funkcję.

1. Uruchom polecenie w następujący sposób, aby utworzyć projekt funkcji `func init` w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:  

    ```console
    func init LocalFunctionProj --python
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
    
    `func new`Tworzy podfolder pasujący do nazwy funkcji, który zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o *nazwiefunction.jsna .*

### <a name="optional-examine-the-file-contents"></a>(Opcjonalnie) Badanie zawartości pliku

W razie potrzeby możesz przejść do [lokalnego](#run-the-function-locally) uruchomienia funkcji i później zbadać zawartość pliku.

#### <a name="__init__py"></a>\_\_init \_ \_ .py

*\_ \_ Plik init \_ \_ .py* zawiera funkcję języka Python, która jest wyzwalana zgodnie z konfiguracją w pliku `main()` *function.jsna .*

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

W przypadku wyzwalacza HTTP funkcja odbiera dane żądania w zmiennej zgodnie z definicją wfunction.js`req` *na .* `req` jest wystąpieniem klasy [azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Obiekt zwracany, zdefiniowany wfunction.js`$return` *,* jest wystąpieniem klasy [azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, [zobacz Azure Functions wyzwalaczy i powiązań HTTP.](./functions-bindings-http-webhook.md?tabs=python)

#### <a name="functionjson"></a>function.json

*function.jsjest* plikiem konfiguracji, który definiuje dane wejściowe i wyjściowe dla funkcji, w tym `bindings` typ wyzwalacza.

W razie potrzeby można `scriptFile` zmienić sposób wywoływania innego pliku języka Python.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu i [`httpTrigger`](functions-bindings-http-webhook-trigger.md) powiązanie wyjściowe typu [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie zasobów obsługi platformy Azure dla funkcji

Przed wdrożeniem kodu funkcji na platformie Azure należy utworzyć trzy zasoby:

- Grupa zasobów, która jest kontenerem logicznym dla powiązanych zasobów.
- Konto magazynu, które przechowuje informacje o stanie i inne informacje o projektach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji mapuje do lokalnego projektu funkcji i umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania i udostępniania zasobów.

Użyj następujących poleceń, aby utworzyć te elementy. Obsługiwane są zarówno interfejs wiersza polecenia platformy Azure, jak i program PowerShell.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do platformy Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Polecenie [az login](/cli/azure/reference-index#az_login) loguje Cię do konta platformy Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) umożliwia wykonanie cię na koncie platformy Azure.

    ---

1. Utwórz grupę zasobów o `AzureFunctionsQuickstart-rg` nazwie w `westeurope` regionie . 

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Polecenie [az group create](/cli/azure/group#az_group_create) tworzy grupę zasobów. Zazwyczaj grupę zasobów i zasoby tworzy się w regionie w pobliżu, używając dostępnego regionu zwróconego z `az account list-locations` polecenia .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Polecenie [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) tworzy grupę zasobów. Zazwyczaj grupę zasobów i zasoby tworzy się w regionie w pobliżu, używając dostępnego regionu zwróconego z polecenia cmdlet [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

    ---

    > [!NOTE]
    > Nie można hostowania aplikacji dla systemów Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie z aplikacją funkcji systemu Windows lub aplikacją internetową, musisz `AzureFunctionsQuickstart-rg` użyć innej grupy zasobów.

1. Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Polecenie [az storage account create](/cli/azure/storage/account#az_storage_account_create) tworzy konto magazynu. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Polecenie cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) tworzy konto magazynu.

    ---

    W poprzednim przykładzie zastąp nazwą, która jest odpowiednia dla Ciebie i `<STORAGE_NAME>` unikatowa w usłudze Azure Storage. Nazwy muszą zawierać tylko cyfry od trzech do 24 znaków i małe litery. `Standard_LRS` Określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).
    
    W przypadku tego przewodnika Szybki start na koncie magazynu naliczane jest tylko kilka centów (USD).

1. Utwórz aplikację funkcji na platformie Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Polecenie [az functionapp create](/cli/azure/functionapp#az_functionapp_create) tworzy aplikację funkcji na platformie Azure. Jeśli używasz języka Python 3.7 lub 3.6, zmień `--runtime-version` odpowiednio na `3.7` lub `3.6` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    Polecenie cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) tworzy aplikację funkcji na platformie Azure. Jeśli używasz języka Python 3.7 lub 3.6, zmień odpowiednio `-RuntimeVersion` na `3.7` lub `3.6` .

    ---
    
    W poprzednim przykładzie zastąp nazwą konta użytego w poprzednim kroku, a zastąp wartością globalnie unikatową `<STORAGE_NAME>` `<APP_NAME>` nazwą odpowiednią dla Ciebie.  `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
    
    To polecenie tworzy aplikację funkcji uruchamianą w środowisku uruchomieniowym określonego języka w ramach planu zużycie usługi [Azure Functions,](consumption-plan.md)który jest bezpłatny dla ilości użycia, które są tutaj naliczane. Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie poniesie żadnych kosztów, dopóki nie zostanie aktywowane.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Uruchom następujące polecenie, aby wyświetlić [](functions-run-local.md#enable-streaming-logs) dzienniki przesyłania strumieniowego niemal w czasie rzeczywistym Application Insights w Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

W osobnym oknie terminalu lub w przeglądarce ponownie wywołaj funkcję zdalną. Pełny dziennik wykonywania funkcji na platformie Azure jest wyświetlany w terminalu. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Łączenie z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Masz problemy? Daj nam znać.](https://aka.ms/python-functions-qs-survey)