---
title: Tworzenie funkcji języka Python z wiersza polecenia dla Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Python z wiersza polecenia i opublikować lokalny projekt w hostingu bez serwera w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 626cff867a336880689373c289087e2332a816ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787453"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Szybki start: tworzenie funkcji języka Python na platformie Azure z wiersza polecenia

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [Program PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

W tym artykule użyjemy narzędzi wiersza polecenia, aby utworzyć funkcję języka Python, która odpowiada na żądania HTTP. Po przetestowaniu kodu lokalnie należy go wdrożyć w <abbr title="Środowisko obliczeniowe środowiska uruchomieniowego, w którym wszystkie szczegóły serwera są niewidoczne dla deweloperów aplikacji, co upraszcza proces wdrażania kodu i zarządzania nimi.">praca bezserwerowa</abbr> środowisko <abbr title="Usługa platformy Azure, która zapewnia ekonomiczne bez serwera środowisko obliczeniowe dla aplikacji.">Azure Functions</abbr>.

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem konta platformy Azure w wysokości nie mniejszej niż kilka centów USD.

Istnieje również Visual Studio Code [tego artykułu.](create-first-function-vs-code-python.md)

## <a name="1-configure-your-environment"></a>1. Konfigurowanie środowiska

Przed rozpoczęciem musisz mieć następujące elementy:

+ Platforma Azure <abbr title="Profil, który przechowuje informacje rozliczeniowe dotyczące użycia platformy Azure.">account</abbr> z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzasz zasobami na platformie Azure, zwykle skojarzona z jednostką lub działem w organizacji.">subskrypcja</abbr>. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#v2) 3.x. 
  
+ Albo <abbr title="Zestaw międzyplatformowych narzędzi wiersza polecenia do pracy z zasobami platformy Azure z lokalnego komputera dewelopera jako alternatywa dla korzystania z Azure Portal.">Interfejs wiersza polecenia platformy Azure</abbr> lub <abbr title="Moduł programu PowerShell, który udostępnia polecenia do pracy z zasobami platformy Azure z lokalnego komputera dewelopera, jako alternatywę dla korzystania z Azure Portal.">Azure PowerShell</abbr> do tworzenia zasobów platformy Azure:

    + [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.4 lub nowszej.

    + [Azure PowerShell](/powershell/azure/install-az-ps) w wersji 5.0 lub nowszej.

+ [Python 3.8 (64-bitowy),](https://www.python.org/downloads/release/python-382/) [Python 3.7 (64-bitowy),](https://www.python.org/downloads/release/python-375/) [Python 3.6 (64-bitowy),](https://www.python.org/downloads/release/python-368/)które są obsługiwane przez wersję 3.x Azure Functions.

### <a name="11-prerequisite-check"></a>1.1 Sprawdzanie wymagań wstępnych

Sprawdź wymagania wstępne, które zależą od tego, czy używasz interfejsu wiersza polecenia platformy Azure, Azure PowerShell do tworzenia zasobów platformy Azure:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

+ W terminalu lub oknie polecenia uruchom polecenie , `func --version` aby sprawdzić, czy <abbr title="Zestaw narzędzi wiersza polecenia do pracy z Azure Functions na komputerze lokalnym.">Azure Functions Core Tools</abbr> to wersja 3.x.

+ Uruchom `az --version` , aby sprawdzić, czy interfejs wiersza polecenia platformy Azure jest w wersji 2.4 lub nowszej.

+ Uruchom, `az login` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ Uruchom system (Linux/macOS) lub (Windows), aby sprawdzić raporty dotyczące wersji języka Python w wersji `python --version` `py --version` 3.8.x, 3.7.x lub 3.6.x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ W terminalu lub oknie polecenia uruchom polecenie , `func --version` aby sprawdzić, czy <abbr title="Zestaw narzędzi wiersza polecenia do pracy z Azure Functions na komputerze lokalnym.">Azure Functions Core Tools</abbr> to wersja 3.x.

+ Uruchom `(Get-Module -ListAvailable Az).Version` i zweryfikuj wersję 5.0 lub nowszą. 

+ Uruchom, `Connect-AzAccount` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ Uruchom system (Linux/macOS) lub (Windows), aby sprawdzić raporty dotyczące wersji języka Python w wersji `python --version` `py --version` 3.8.x, 3.7.x lub 3.6.x.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> Tworzenie i aktywowanie środowiska wirtualnego

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

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. Tworzenie lokalnego projektu funkcji

W tej sekcji utworzysz lokalną <abbr title="Kontener logiczny dla co najmniej jednej funkcji, która może być wdrażana i zarządzana razem.">Azure Functions projektu</abbr> w języku Python. Każda funkcja w projekcie odpowiada na określony <abbr title="Typ zdarzenia, które wywołuje kod funkcji, taki jak żądanie HTTP, komunikat w kolejce lub określony czas.">Wyzwalacz</abbr>.

1. Uruchom polecenie `func init` , aby utworzyć projekt funkcji w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Przejdź do folderu projektu:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>Co utworzono w folderze LocalFunctionProj?</strong></summary>
    
    Ten folder zawiera różne pliki projektu, w tym pliki konfiguracji o [ nazwachlocal.settings.jsna](functions-run-local.md#local-settings-file) ihost.js[ na](functions-host-json.md). Ponieważ *local.settings.jsmoże* zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *gitignore.*
    </details>

1. Dodaj funkcję do projektu przy użyciu następującego polecenia:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    `--name`Argument jest unikatową nazwą funkcji (HttpExample).

    Argument `--template` określa wyzwalacz funkcji (HTTP).
    
    `func new`Tworzy podfolder zgodny z nazwą funkcji, który zawiera plik *\_ \_ init \_ \_ .py* z kodem funkcji i plikiem konfiguracji o *nazwiefunction.jspliku*.

    <br/>    
    <details>
    <summary><strong>Kod __init__.py</strong></summary>
    
    *\_ \_ Plik init \_ \_ .py* zawiera funkcję języka Python, która jest wyzwalana zgodnie z konfiguracją w pliku `main()` *function.jsna .*
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    W przypadku wyzwalacza HTTP funkcja odbiera dane żądania w zmiennej zgodnie z definicją wfunction.js`req` *na .* `req` jest wystąpieniem klasy [azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Obiekt return, zdefiniowany tak jak wfunction.js, jest wystąpieniem klasy `$return` [azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse).  Aby dowiedzieć się więcej, [zobacz Azure Functions http triggers and bindings (Wyzwalacze](./functions-bindings-http-webhook.md?tabs=python)i powiązania HTTP).
    </details>

    <br/>
    <details>
    <summary><strong>Kod dla function.jsna</strong></summary>

    *function.jsjest* plikiem konfiguracji, który definiuje <abbr title="Połączenia deklaratywne między funkcją i innymi zasobami. Powiązanie wejściowe dostarcza dane do funkcji; Powiązanie wyjściowe dostarcza dane z funkcji do innych zasobów.">powiązania wejściowe i wyjściowe</abbr> dla funkcji, w tym typu wyzwalacza.
    
    W razie potrzeby możesz `scriptFile` zmienić , aby wywołać inny plik języka Python.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu i powiązanie [`httpTrigger`](functions-bindings-http-webhook-trigger.md) wyjściowe typu [`http`](functions-bindings-http-webhook-output.md) .    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Uruchom funkcję lokalnie

1. Uruchom funkcję przez uruchomienie lokalnego Azure Functions uruchomieniowego z folderu *LocalFunctionProj:*

    ```
    func start
    ```

    Pod koniec danych wyjściowych powinny pojawić się następujące wiersze: 
    
    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    <br/>
    <details>
    <summary><strong>Nie widzę próbki HttpExample w danych wyjściowych</strong></summary>

    Jeśli nie zostanie wyświetlony przykład HttpExample, prawdopodobnie uruchomiliśmy hosta spoza folderu głównego projektu. W takim przypadku użyj <kbd>klawiszy Ctrl+C,</kbd> aby zatrzymać hosta, przejść do folderu głównego projektu i ponownie uruchomić poprzednie polecenie.
    </details>

1. Skopiuj adres URL funkcji **HttpExample** z tych danych wyjściowych do przeglądarki i dołącz ciąg zapytania **?name=<YOUR_NAME>**, dzięki czemu pełny adres URL, taki jak **http://localhost:7071/api/HttpExample?name=Functions** . W przeglądarce powinien zostać wyświetlony komunikat, taki **jak Hello Functions:**

    ![Wynik działania funkcji lokalnie w przeglądarce](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Terminal, w którym uruchomiliśmy projekt, pokazuje również dane wyjściowe dziennika podczas żądań.

1. Gdy wszystko będzie gotowe, naciśnij klawisze <kbd>Ctrl+C</kbd> i wybierz <kbd>pozycję y,</kbd> aby zatrzymać hosta funkcji.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Tworzenie zasobów obsługi platformy Azure dla funkcji

Przed wdrożeniem kodu funkcji na platformie Azure należy utworzyć <abbr title="Kontener logiczny dla powiązanych zasobów platformy Azure, które można zarządzać jako jednostką.">grupa zasobów</abbr>A <abbr title="Konto, które zawiera wszystkie obiekty danych usługi Azure Storage. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych magazynu.">konto magazynu</abbr>, i <abbr title="Zasób w chmurze, który hostuje funkcje bez serwera na platformie Azure, który zapewnia bazowe środowisko obliczeniowe, w którym są uruchamiane funkcje.">aplikacja funkcji</abbr> za pomocą następujących poleceń:

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
 
    Polecenie [az group create](/cli/azure/group#az_group_create) tworzy grupę zasobów. Zazwyczaj tworzysz grupę zasobów i zasoby w <abbr title="Geograficzne odwołanie do określonego centrum danych platformy Azure, w którym są przydzielane zasoby.">region</abbr> w pobliżu, używając dostępnego regionu zwróconego z `az account list-locations` polecenia .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Polecenie [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) tworzy grupę zasobów. Zazwyczaj grupę zasobów i zasoby tworzy się w regionie w pobliżu, używając dostępnego regionu zwróconego z polecenia cmdlet [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

    ---

    Nie można hostowania aplikacji dla systemów Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie z aplikacją funkcji systemu Windows lub aplikacją internetową, musisz `AzureFunctionsQuickstart-rg` użyć innej grupy zasobów.

1. Utwórz konto usługi Azure Storage ogólnego przeznaczenia w grupie zasobów i regionie:

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

    Zastąp `<STORAGE_NAME>` nazwą, która jest odpowiednia dla Ciebie, i <abbr title="Nazwa musi być unikatowa dla wszystkich kont magazynu używanych globalnie przez wszystkich klientów platformy Azure. Można na przykład użyć kombinacji nazwy osobistej lub firmowej, nazwy aplikacji i identyfikatora liczbowego, jak w przypadku contosobizappstorage20.">unikatowa w usłudze Azure Storage</abbr>. Nazwy muszą zawierać tylko cyfry od trzech do 24 znaków i małe litery. `Standard_LRS` Określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).
    
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
    
    Polecenie cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) tworzy aplikację funkcji na platformie Azure. Jeśli używasz języka Python 3.7 lub 3.6, zmień `-RuntimeVersion` odpowiednio na `3.7` lub `3.6` .

    ---
    
    Zastąp `<STORAGE_NAME>` nazwą konta, które było używane w poprzednim kroku.

    `<APP_NAME>`Zamień na <abbr title="Nazwa, która musi być unikatowa w skali globalnej dla wszystkich klientów platformy Azure. Można na przykład użyć kombinacji nazwy osobistej lub organizacji, nazwy aplikacji i identyfikatora liczbowego, jak w przypadku contoso-bizapp-func-20.">globalnie unikatowa nazwa odpowiednia dla Ciebie</abbr>. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
    
    <br/>
    <details>
    <summary><strong>Jaki jest koszt zasobów aprowowanych na platformie Azure?</strong></summary>

    To polecenie tworzy aplikację funkcji uruchamianą w określonym środowisku uruchomieniowym języka w ramach [planu Azure Functions Consumption ,](functions-scale.md#overview-of-plans)który jest bezpłatny dla ilości użycia, które zostanie naliczone w tym miejscu. Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitorowanie Azure Functions](functions-monitoring.md). Wystąpienie nie ponosi żadnych kosztów, dopóki nie zostanie aktywowane.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Wdrażanie projektu funkcji na platformie Azure

Po pomyślnym utworzeniu aplikacji funkcji na platformie Azure  możesz teraz wdrożyć projekt funkcji lokalnych za pomocą polecenia [func azure functionapp publish.](functions-run-local.md#project-file-deployment)  

W poniższym przykładzie zastąp `<APP_NAME>` nazwą swojej aplikacji.

```console
func azure functionapp publish <APP_NAME>
```

Polecenie wyświetla wyniki podobne do następujących danych wyjściowych `publish` (obciętych dla uproszczenia):

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. Wywoływanie funkcji na platformie Azure

Ponieważ funkcja używa wyzwalacza HTTP, wywołujesz go, wysyłasz żądanie HTTP do jej adresu URL w przeglądarce lub za pomocą narzędzia takiego jak <abbr title="Narzędzie wiersza polecenia do generowania żądań HTTP do adresu URL; Zobacz https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Przeglądarka](#tab/browser)

Skopiuj pełny adres **URL** wywołania widoczny w danych wyjściowych polecenia na pasku adresu przeglądarki, dołączając parametr zapytania `publish`&**name=Functions.** W przeglądarce powinny być wyświetlane podobne dane wyjściowe, jak w przypadku lokalnego działania funkcji.

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Uruchom [`curl`](https://curl.haxx.se/) z adresem URL **wywołania**, dołączając parametr **&name=Functions.** Dane wyjściowe polecenia powinny mieć tekst "Hello Functions".

![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu narzędzia curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 Wyświetlanie dzienników przesyłania strumieniowego w czasie rzeczywistym

Uruchom następujące polecenie, aby wyświetlić [](functions-run-local.md#enable-streaming-logs) dzienniki przesyłania strumieniowego niemal w czasie rzeczywistym Application Insights w Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Zastąp `<APP_NAME>` nazwą swojej aplikacji funkcji.

W osobnym oknie terminalu lub w przeglądarce ponownie wywołaj funkcję zdalną. Pełny dziennik wykonywania funkcji na platformie Azure jest wyświetlany w terminalu. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Czyszczenie zasobów

Jeśli przejdź do [następnego kroku i](#next-steps) dodasz <abbr title="Oznacza skojarzenie funkcji z kolejką magazynu, aby można było tworzyć komunikaty w kolejce. ">Powiązanie wyjściowe kolejki usługi Azure Storage</abbr>, zachowaj wszystkie zasoby, ponieważ będziesz tworzyć na podstawie już wykonanej pracy.

W przeciwnym razie użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

 # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Łączenie z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Masz problemy? Daj nam znać.](https://aka.ms/python-functions-qs-survey)
