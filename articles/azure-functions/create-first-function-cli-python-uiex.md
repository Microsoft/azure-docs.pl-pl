---
title: Utwórz funkcję języka Python z poziomu wiersza polecenia dla Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Python z poziomu wiersza polecenia i opublikować projekt lokalny na potrzeby hostingu bezserwerowego w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 76ec99e5f892438aaa637b15bb7382843d90f334
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748785"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Szybki Start: Tworzenie funkcji języka Python na platformie Azure z poziomu wiersza polecenia

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [Program PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji języka Python, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go na <abbr title="Środowisko obliczeniowe środowiska uruchomieniowego, w którym wszystkie szczegóły serwera są niewidoczne dla deweloperów aplikacji, co upraszcza proces wdrażania kodu i zarządzania nim.">praca bezserwerowa</abbr> Środowisko programu <abbr title="Usługa platformy Azure, która zapewnia nieekonomiczne środowisko obliczeniowe dla aplikacji.">Azure Functions</abbr>.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

Istnieje również oparta na [Visual Studio Code wersja](create-first-function-vs-code-python.md) tego artykułu.

## <a name="1-configure-your-environment"></a>1. Skonfiguruj środowisko

Przed rozpoczęciem należy wykonać następujące czynności:

+ Platforma Azure <abbr title="Profil, który przechowuje informacje o rozliczeniach dotyczące użycia platformy Azure.">account</abbr> z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzane są zasoby na platformie Azure, zwykle skojarzone z osobą lub działem w organizacji.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) w wersji 3. x. 
  
+ Albo <abbr title="Zestaw wieloplatformowych narzędzi wiersza polecenia do pracy z zasobami platformy Azure z lokalnego komputera deweloperskiego, alternatywą dla korzystania z Azure Portal.">Interfejs wiersza polecenia platformy Azure</abbr> lub <abbr title="Moduł programu PowerShell, który udostępnia polecenia do pracy z zasobami platformy Azure z lokalnego komputera deweloperskiego, jako alternatywę dla korzystania z Azure Portal.">Azure PowerShell</abbr> w przypadku tworzenia zasobów platformy Azure:

    + [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2,4 lub nowszej.

    + [Azure PowerShell](/powershell/azure/install-az-ps) w wersji 5,0 lub nowszej.

+ [Python 3,8 (64-bitowy)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), które są obsługiwane przez wersję 3. x Azure Functions.

### <a name="11-prerequisite-check"></a>1,1 Sprawdzanie wymagań wstępnych

Sprawdź wymagania wstępne, które zależą od tego, czy używasz interfejsu wiersza polecenia platformy Azure, czy Azure PowerShell do tworzenia zasobów platformy Azure:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

+ W terminalu lub oknie poleceń Uruchom polecenie, `func --version` Aby sprawdzić, czy <abbr title="Zestaw narzędzi wiersza polecenia do pracy z Azure Functions na komputerze lokalnym.">Azure Functions Core Tools</abbr> są w wersji 3. x.

+ Uruchom, `az --version` Aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure to 2,4 lub nowszego.

+ Uruchom, `az login` Aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ Uruchom polecenie `python --version` (Linux/macOS) lub `py --version` (Windows), aby sprawdzić raporty wersji języka Python 3.8. x, 3.7. x lub 3.6. x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ W terminalu lub oknie poleceń Uruchom polecenie, `func --version` Aby sprawdzić, czy Azure Functions Core Tools są w wersji 3. x.

+ Uruchom `(Get-Module -ListAvailable Az).Version` i sprawdź wersję 5,0 lub nowszą. 

+ Uruchom, `Connect-AzAccount` Aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ Uruchom polecenie `python --version` (Linux/macOS) lub `py --version` (Windows), aby sprawdzić raporty wersji języka Python 3.8. x, 3.7. x lub 3.6. x.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> Tworzenie i aktywowanie środowiska wirtualnego

W odpowiednim folderze Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv` . Upewnij się, że używasz języka Python 3,8, 3,7 lub 3,6, który jest obsługiwany przez Azure Functions.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Jeśli środowisko Python nie zainstalowało pakietu venv na dystrybucji systemu Linux, uruchom następujące polecenie:

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

## <a name="3-create-a-local-function-project"></a>3. Utwórz projekt funkcji lokalnej

W tej sekcji utworzysz lokalną <abbr title="Kontener logiczny dla jednej lub kilku funkcji, które mogą być wdrażane i zarządzane wspólnie.">Projekt Azure Functions</abbr> w języku Python. Każda funkcja w projekcie odpowiada określonej <abbr title="Typ zdarzenia, które wywołuje kod funkcji, taki jak żądanie HTTP, komunikat kolejki lub określony czas.">uruchamiać</abbr>.

1. Uruchom `func init` polecenie, aby utworzyć projekt Functions w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Przejdź do folderu projektu:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>Co jest tworzone w folderze LocalFunctionProj?</strong></summary>
    
    Ten folder zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [local.settings.js](functions-run-local.md#local-settings-file) i [host.jsna](functions-host-json.md). Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *. gitignore* .
    </details>

1. Dodaj funkcję do projektu za pomocą następującego polecenia:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    `--name`Argument jest unikatową nazwą funkcji (HttpExample).

    `--template`Argument określa wyzwalacz funkcji (http).
    
    `func new`tworzy podfolder pasujący do nazwy funkcji zawierającej plik *\_ \_ init \_ \_ . PR* z kodem funkcji i plikiem konfiguracji o nazwie *function.json*.

    <br/>    
    <details>
    <summary><strong>Kod dla __init__. PR</strong></summary>
    
    *\_ \_ init \_ \_ . PR* zawiera `main()` funkcję języka Python, która jest wyzwalana zgodnie z konfiguracją w *function.jsna*.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    Dla wyzwalacza HTTP funkcja otrzymuje dane żądania w zmiennej `req` , zgodnie z definicją w *function.json*. `req` jest wystąpieniem [klasy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Obiekt zwracany, zdefiniowany jako `$return` w *function.json*, jest wystąpieniem [klasy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>Kod dla function.jsna</strong></summary>

    *function.json* to plik konfiguracji, który definiuje <abbr title="Deklaratywne połączenia między funkcją i innymi zasobami. Powiązanie danych wejściowych zawiera dane do funkcji; powiązanie danych wyjściowych udostępnia dane z funkcji do innych zasobów.">powiązania wejściowe i wyjściowe</abbr> dla funkcji, w tym typ wyzwalacza.
    
    `scriptFile`W razie potrzeby można zmienić w taki sposób, aby wywoływać inny plik Python.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu [`httpTrigger`](functions-bindings-http-webhook-trigger.md) i powiązania danych wyjściowych typu [`http`](functions-bindings-http-webhook-output.md) .    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Uruchom funkcję lokalnie

1. Uruchom funkcję przez uruchomienie lokalnego hosta Azure Functions środowiska uruchomieniowego z folderu *LocalFunctionProj* :

    ```
    func start
    ```

    W kierunku końca danych wyjściowych powinny być wyświetlane następujące wiersze: 
    
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
    <summary><strong>Nie widzę HttpExample w danych wyjściowych</strong></summary>

    Jeśli HttpExample nie pojawia się, prawdopodobnie uruchomiono hosta spoza folderu głównego projektu. W takim przypadku użyj <kbd>kombinacji klawiszy CTRL + C</kbd> , aby zatrzymać hosta, przejdź do folderu głównego projektu i ponownie uruchom poprzednie polecenie.
    </details>

1. Skopiuj adres URL funkcji **HttpExample** z tego danych wyjściowych do przeglądarki i dołącz ciąg zapytania **? name =<YOUR_NAME>**, wprowadzając pełny adres URL, taki jak **http://localhost:7071/api/HttpExample?name=Functions** . W przeglądarce powinien zostać wyświetlony komunikat podobny do **funkcji Hello**:

    ![Wynik funkcji uruchomionej lokalnie w przeglądarce](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Na terminalu, w którym rozpoczął się projekt, są również wyświetlane dane wyjściowe dziennika podczas zgłaszania żądań.

1. Gdy skończysz, użyj <kbd>kombinacji klawiszy CTRL + C</kbd> i wybierz pozycję <kbd>y</kbd> , aby zatrzymać host funkcji.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Utwórz obsługę zasobów platformy Azure dla swojej funkcji

Aby można było wdrożyć kod funkcji na platformie Azure, należy utworzyć <abbr title="Logiczny kontener dla powiązanych zasobów platformy Azure, którymi można zarządzać jako jednostką.">grupa zasobów</abbr>, a <abbr title="Konto, które zawiera wszystkie obiekty danych usługi Azure Storage. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych magazynu.">konto magazynu</abbr>i a <abbr title="Zasób w chmurze, który obsługuje funkcje bezserwerowe na platformie Azure, która zapewnia podstawowe środowisko obliczeniowe, w którym uruchamiane są funkcje.">Aplikacja funkcji</abbr> za pomocą następujących poleceń:

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do platformy Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Polecenie [AZ login umożliwia zalogowanie](/cli/azure/reference-index#az-login) się do konta platformy Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) umożliwia zalogowanie się do konta platformy Azure.

    ---

1. Utwórz grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` w `westeurope` regionie. 

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Polecenie [AZ Group Create](/cli/azure/group#az-group-create) tworzy grupę zasobów. Zazwyczaj tworzysz grupę zasobów i zasoby w <abbr title="Odniesienia geograficzne do określonego centrum danych platformy Azure, w którym są przydzielono zasoby.">region</abbr> w najbliższej postaci, używając dostępnego regionu zwróconego przez `az account list-locations` polecenie.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Polecenie [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) tworzy grupę zasobów. Zazwyczaj tworzysz grupę zasobów i zasoby w regionie, w którym wiesz, przy użyciu dostępnego regionu zwróconego przez polecenie cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

    ---

    Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` przy użyciu aplikacji funkcji systemu Windows lub aplikacji sieci Web, musisz użyć innej grupy zasobów.

1. Utwórz konto usługi Azure Storage ogólnego przeznaczenia w swojej grupie zasobów i regionie:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Polecenie [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create) tworzy konto magazynu. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Polecenie cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) tworzy konto magazynu.

    ---

    Zamień na `<STORAGE_NAME>` nazwę, która jest odpowiednia dla Ciebie i <abbr title="Nazwa musi być unikatowa dla wszystkich kont magazynu używanych przez wszystkich klientów platformy Azure globalnie. Można na przykład użyć kombinacji nazwy prywatnej lub firmowej, nazwy aplikacji i identyfikatora numerycznego, jak w contosobizappstorage20">unikatowa w usłudze Azure Storage</abbr>. Nazwy muszą zawierać od 3 do 24 znaków cyfry i małe litery. `Standard_LRS` Określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).
    
    W tym przewodniku szybki start konto magazynu odnosi się tylko do kilku centów (USD).

1. Tworzenie aplikacji funkcji na platformie Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Polecenie [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) tworzy aplikację funkcji na platformie Azure. Jeśli używasz języka Python 3,7 lub 3,6, odpowiednio zmień `--runtime-version` na `3.7` lub `3.6` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    Polecenie cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) tworzy aplikację funkcji na platformie Azure. Jeśli używasz języka Python 3,7 lub 3,6, odpowiednio zmień `-RuntimeVersion` na `3.7` lub `3.6` .

    ---
    
    Zamień `<STORAGE_NAME>` na nazwę konta użytego w poprzednim kroku.

    Zamień `<APP_NAME>` na <abbr title="Nazwa, która musi być unikatowa dla wszystkich klientów platformy Azure globalnie. Można na przykład użyć kombinacji nazwy prywatnej lub organizacji, nazwy aplikacji i identyfikatora liczbowego, tak jak w contoso-bizapp-Func-20.">wybrana globalnie unikatowa nazwa</abbr>. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
    
    <br/>
    <details>
    <summary><strong>Jaki jest koszt zasobów zainicjowanych na platformie Azure?</strong></summary>

    To polecenie tworzy aplikację funkcji działającą w określonym środowisku uruchomieniowym języka zgodnie z [planem zużycia Azure Functions](functions-scale.md#overview-of-plans), który jest bezpłatny dla nakładu pracy w tym miejscu. Polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą którego można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie wiąże się z żadnymi kosztami, dopóki nie zostanie uaktywnione.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Wdróż projekt funkcji na platformie Azure

Po pomyślnym utworzeniu aplikacji funkcji na platformie Azure Możesz teraz przystąpić do **wdrażania projektu funkcji lokalnych** przy użyciu polecenia [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) .  

W poniższym przykładzie Zastąp `<APP_NAME>` nazwą swojej aplikacji.

```console
func azure functionapp publish <APP_NAME>
```

`publish`Polecenie wyświetla wyniki podobne do następujących danych wyjściowych (obcięty dla prostoty):

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

## <a name="7-invoke-the-function-on-azure"></a>7. wywołaj funkcję na platformie Azure

Ponieważ funkcja używa wyzwalacza HTTP, wywołuje ją, wysyłając żądanie HTTP do jego adresu URL w przeglądarce lub za pomocą narzędzia takiego jak <abbr title="Narzędzie wiersza polecenia do generowania żądań HTTP na adres URL; wyświetlania https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Przeglądarka](#tab/browser)

Skopiuj pełny **adres URL Wywołaj** pokazany w danych wyjściowych `publish` polecenia na pasku adresu przeglądarki, dołączając parametr zapytania **&Name = Functions**. Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak w przypadku lokalnego uruchomienia funkcji.

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Uruchom [`curl`](https://curl.haxx.se/) polecenie **Invoke URL**, dołączając parametr **&Name = Functions**. Danymi wyjściowymi polecenia powinien być tekst "Hello Functions".

![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu programu zwinięcie](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7,1 Wyświetlanie dzienników przesyłania strumieniowego w czasie rzeczywistym

Uruchom następujące polecenie, aby wyświetlić [dzienniki przesyłania strumieniowego](functions-run-local.md#enable-streaming-logs) niemal w czasie rzeczywistym w Application Insights w Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Zamień `<APP_NAME>` na nazwę aplikacji funkcji.

W osobnym oknie terminalu lub w przeglądarce wywołaj funkcję zdalną ponownie. W terminalu zostanie wyświetlony pełny dziennik wykonywania funkcji na platformie Azure. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Wyczyść zasoby

Jeśli przejdziesz do [następnego kroku](#next-steps) i dodasz <abbr title="Oznacza to, że funkcja jest skojarzona z kolejką magazynu, dzięki czemu może tworzyć komunikaty w kolejce. ">Powiązanie danych wyjściowych kolejki usługi Azure Storage</abbr>Zachowaj wszystkie Twoje zasoby w miarę kompilowania już wykonanej pracy.

W przeciwnym razie użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

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
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Masz problemy? Daj nam znać.](https://aka.ms/python-functions-qs-survey)
