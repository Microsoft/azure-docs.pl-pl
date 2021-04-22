---
title: Tworzenie funkcji języka C# z wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka C# z wiersza polecenia, a następnie opublikować lokalny projekt w hostingu bez serwera w Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 45173a74c0e3189c1f356aea2f8024ff15409f32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866201"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Szybki start: tworzenie funkcji języka C# na platformie Azure z wiersza polecenia

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [Program PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

W tym artykule użyjemy narzędzi wiersza polecenia, aby utworzyć funkcję klasy języka C# opartą na bibliotece, która odpowiada na żądania HTTP. Po przetestowaniu kodu lokalnie należy go wdrożyć w <abbr title="Środowisko obliczeniowe środowiska uruchomieniowego, w którym wszystkie szczegóły serwera są niewidoczne dla deweloperów aplikacji, co upraszcza proces wdrażania kodu i zarządzania nimi.">praca bezserwerowa</abbr> środowisko <abbr title="Usługa platformy Azure, która zapewnia ekonomiczne bez serwera środowisko obliczeniowe dla aplikacji.">Azure Functions</abbr>.

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem konta platformy Azure o wartości kilku centów lub mniej.

Istnieje również wersja [Visual Studio Code tego](create-first-function-vs-code-csharp.md) artykułu oparta na języku.

## <a name="1-prepare-your-environment"></a>1. Przygotowywanie środowiska

+ Uzyskiwanie konta platformy Azure <abbr title="Profil, który przechowuje informacje rozliczeniowe dotyczące użycia platformy Azure.">account</abbr> z aktywną <abbr title="Podstawowa struktura organizacyjna, w której zarządzasz zasobami na platformie Azure, zwykle skojarzona z jednostką lub działem w organizacji.">subskrypcja</abbr>. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Instalowanie zestawu SDK platformy [.NET Core 3.1](https://dotnet.microsoft.com/download)

+ Zainstaluj [Azure Functions Core Tools](functions-run-local.md#v2) wersji 3.x.

+ Albo <abbr title="Zestaw międzyplatformowych narzędzi wiersza polecenia do pracy z zasobami platformy Azure z lokalnego komputera dewelopera jako alternatywa dla korzystania z Azure Portal.">Interfejs wiersza polecenia platformy Azure</abbr> lub <abbr title="Moduł programu PowerShell, który udostępnia polecenia do pracy z zasobami platformy Azure z lokalnego komputera dewelopera, jako alternatywę dla korzystania z Azure Portal.">Azure PowerShell</abbr> do tworzenia zasobów platformy Azure:

    + [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.4 lub nowszej.

    + [Azure PowerShell](/powershell/azure/install-az-ps) wersji 5.0 lub nowszej.

---

### <a name="2-verify-prerequisites"></a>2. Weryfikowanie wymagań wstępnych

Sprawdź wymagania wstępne, które zależą od tego, czy używasz interfejsu wiersza polecenia platformy Azure, czy Azure PowerShell do tworzenia zasobów platformy Azure:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

+ W terminalu lub oknie polecenia uruchom polecenie , `func --version` aby sprawdzić, czy <abbr title="Zestaw narzędzi wiersza polecenia do pracy z Azure Functions na komputerze lokalnym.">Azure Functions Core Tools</abbr> to wersja 3.x.

+ **Uruchom** `az --version` Aby sprawdzić, czy interfejs wiersza polecenia platformy Azure jest w wersji 2.4 lub nowszej.

+ **Uruchom** `az login` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ **Uruchom** `dotnet --list-sdks` aby sprawdzić, zestaw .NET Core SDK zainstalowana wersja 3.1.x

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Uruchom** `func --version` Aby sprawdzić, czy Azure Functions Core Tools są w wersji 3.x.

+ **Uruchom** `(Get-Module -ListAvailable Az).Version` i sprawdź wersję 5.0 lub nowszą. 

+ **Uruchom** `Connect-AzAccount` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ **Uruchom** `dotnet --list-sdks` aby sprawdzić, zestaw .NET Core SDK zainstalowana wersja 3.1.x

---

## <a name="3-create-a-local-function-project"></a>3. Tworzenie lokalnego projektu funkcji

W tej sekcji utworzysz lokalną <abbr title="Kontener logiczny dla co najmniej jednej funkcji, która może być wdrażana i zarządzana razem.">Azure Functions projektu</abbr> w języku C#. Każda funkcja w projekcie odpowiada na określoną <abbr title="Zdarzenie, które wywołuje kod funkcji, takie jak żądanie HTTP, komunikat w kolejce lub określony czas.">Wyzwalacz</abbr>.

1. Uruchom polecenie `func init` , aby utworzyć projekt funkcji w folderze o nazwie *LocalFunctionProj z* określonym środowiskiem uruchomieniowym:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Uruchom** "cd LocalFunctionProj", aby przejść do <abbr title="Ten folder zawiera różne pliki projektu, w tym pliki konfiguracji o local.settings.jsi host.jsna. Ponieważ local.settings.jsmoże zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku .gitignore.">Folder projektu</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Dodaj funkcję do projektu przy użyciu następującego polecenia:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    `--name`Argument jest unikatową nazwą funkcji (HttpExample).

    Argument `--template` określa wyzwalacz funkcji (HTTP).

    
    <br/>   
    <details>  
    <summary><strong>Opcjonalnie: kod dla httpExample.cs</strong></summary>  
    
    *Plik HttpExample.cs* zawiera metodę, która odbiera dane żądania w zmiennej , jest elementem HttpRequest, który jest dekorowany za pomocą wartości `Run` `req` **HttpTriggerAttribute,** [](/dotnet/api/microsoft.aspnetcore.http.httprequest) która definiuje zachowanie wyzwalacza.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    Obiekt zwracany jest [elementem ActionResult,](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) który zwraca komunikat odpowiedzi w następujący sposób: [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) lub [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Aby dowiedzieć się więcej, [zobacz Azure Functions http triggers and bindings (Wyzwalacze](./functions-bindings-http-webhook.md?tabs=csharp)i powiązania HTTP).  
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Uruchom funkcję lokalnie

1. Uruchom funkcję, uruchamiać lokalnego hosta Azure Functions uruchomieniowego z folderu *LocalFunctionProj:*

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
    <summary><strong>Nie widzę programu HttpExample w danych wyjściowych</strong></summary>

    Jeśli nie zostanie wyświetlony przykład HttpExample, prawdopodobnie uruchomiliśmy hosta spoza folderu głównego projektu. W takim przypadku użyj <kbd>klawiszy Ctrl+C,</kbd> aby zatrzymać hosta, przejść do folderu głównego projektu i ponownie uruchomić poprzednie polecenie.
    </details>

1. Skopiuj adres URL funkcji **HttpExample** z tych danych wyjściowych do przeglądarki i dołącz ciąg zapytania **?name=<YOUR_NAME>**, dzięki czemu pełny adres URL, taki jak **http://localhost:7071/api/HttpExample?name=Functions** . W przeglądarce powinien zostać wyświetlony komunikat, taki **jak Hello Functions:**

    ![Wynik działania funkcji lokalnie w przeglądarce](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)


1. Naciśnij  <kbd>klawisze Ctrl+C</kbd> i wybierz <kbd>pozycję y,</kbd> aby zatrzymać hosta funkcji.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Tworzenie zasobów obsługi platformy Azure dla funkcji

Przed wdrożeniem kodu funkcji na platformie Azure należy utworzyć <abbr title="Kontener logiczny dla powiązanych zasobów platformy Azure, które można zarządzać jako jednostką.">grupa zasobów</abbr>A <abbr title="Konto, które zawiera wszystkie obiekty danych usługi Azure Storage. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych magazynu.">konto magazynu</abbr>, i <abbr title="Zasób w chmurze, który hostuje funkcje bez serwera na platformie Azure, który zapewnia bazowe środowisko obliczeniowe, w którym są uruchamiane funkcje.">aplikacja funkcji</abbr> za pomocą następujących poleceń:

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do platformy Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


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


    ---

    Nie można hostowania aplikacji systemów Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie z aplikacją funkcji lub aplikacją internetową systemu `AzureFunctionsQuickstart-rg` Windows, musisz użyć innej grupy zasobów.
    
1. Utwórz konto usługi Azure Storage ogólnego przeznaczenia w grupie zasobów i regionie:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Zastąp `<STORAGE_NAME>` nazwą, która jest odpowiednia dla Ciebie, i <abbr title="Nazwa musi być unikatowa dla wszystkich kont magazynu używanych globalnie przez wszystkich klientów platformy Azure. Na przykład można użyć kombinacji nazwy osobistej lub firmowej, nazwy aplikacji i identyfikatora liczbowego, jak w przypadku contosobizappstorage20">unikatowa w usłudze Azure Storage</abbr>. Nazwy muszą zawierać od trzech do 24 znaków tylko cyfry i małe litery. `Standard_LRS`Określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcję .](storage-considerations.md#storage-account-requirements)


1. Utwórz aplikację funkcji na platformie Azure.
**Zamień** "<STORAGE_NAME>** nazwą w poprzednim kroku.
**Zamień** "<APP_NAME>" z globalnie unikatową nazwą.

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    Zastąp `<STORAGE_NAME>` nazwą konta użytego w poprzednim kroku.

    `<APP_NAME>`Zamień na <abbr title="Nazwa, która musi być unikatowa w skali globalnej dla wszystkich klientów platformy Azure. Można na przykład użyć kombinacji nazwy osobistej lub organizacji, nazwy aplikacji i identyfikatora liczbowego, jak w przypadku contoso-bizapp-func-20.">unikatowa nazwa</abbr>. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 

    <br/>
    <details>
    <summary><strong>Jaki jest koszt zasobów aprowowanych na platformie Azure?</strong></summary>

    To polecenie tworzy aplikację funkcji uruchamianą w określonym środowisku uruchomieniowym języka w ramach [planu Azure Functions Consumption](consumption-plan.md), który jest bezpłatny dla ilości użycia naliczonego w tym miejscu. Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitorowanie Azure Functions](functions-monitoring.md). Wystąpienie nie ponosi żadnych kosztów, dopóki nie zostanie aktywowane.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Wdrażanie projektu funkcji na platformie Azure


**Kopiuj** " func azure funtionapp publish <APP_NAME> into your terminal **Replace** with the name of your app (Opublikuj w terminalu zastąp `<APP_NAME>` nazwą swojej aplikacji).
**Uruchom**

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

Skopiuj pełny adres **URL wywołania** widoczny w danych wyjściowych polecenia na pasek `publish` adresu przeglądarki. **Dołącz** parametr zapytania, **&name=Functions.** 

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Czyszczenie zasobów

Jeśli dodasz dane wyjściowe [kolejki usługi](#next-steps) Azure Storage do następnego kroku <abbr title="Deklaratywne połączenie między funkcją i innymi zasobami. Powiązanie wejściowe dostarcza dane do funkcji; Powiązanie wyjściowe dostarcza dane z funkcji do innych zasobów.">powiązanie</abbr>, zachowaj wszystkie zasoby na miejscu, ponieważ będziesz tworzyć na podstawie już wykonanej pracy.

W przeciwnym razie użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć dodatkowych kosztów.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
