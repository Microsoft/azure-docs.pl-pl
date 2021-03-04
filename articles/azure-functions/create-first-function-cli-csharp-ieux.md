---
title: Tworzenie funkcji języka C# przy użyciu wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka C# z poziomu wiersza polecenia, a następnie opublikować projekt lokalny na potrzeby hostingu bezserwerowego w Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a78abea5bcc5925cb2e137d918c7217ae92b118e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044327"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Szybki Start: Tworzenie funkcji języka C# na platformie Azure z poziomu wiersza polecenia

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [Program PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji opartej na bibliotece klasy C#, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go na <abbr title="Środowisko obliczeniowe środowiska uruchomieniowego, w którym wszystkie szczegóły serwera są niewidoczne dla deweloperów aplikacji, co upraszcza proces wdrażania kodu i zarządzania nim.">praca bezserwerowa</abbr> Środowisko programu <abbr title="Usługa platformy Azure, która zapewnia nieekonomiczne środowisko obliczeniowe dla aplikacji.">Azure Functions</abbr>.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

Istnieje również oparta na [Visual Studio Code wersja](create-first-function-vs-code-csharp.md) tego artykułu.

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

+ Pobierz platformę Azure <abbr title="Profil, który przechowuje informacje o rozliczeniach dotyczące użycia platformy Azure.">account</abbr> z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzane są zasoby na platformie Azure, zwykle skojarzone z osobą lub działem w organizacji.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Zainstaluj [zestaw .NET Core SDK 3,1](https://www.microsoft.com/net/download)

+ Zainstaluj program [Azure Functions Core Tools](functions-run-local.md#v2) w wersji 3. x.

+ Albo <abbr title="Zestaw wieloplatformowych narzędzi wiersza polecenia do pracy z zasobami platformy Azure z lokalnego komputera deweloperskiego, alternatywą dla korzystania z Azure Portal.">Interfejs wiersza polecenia platformy Azure</abbr> lub <abbr title="Moduł programu PowerShell, który udostępnia polecenia do pracy z zasobami platformy Azure z lokalnego komputera deweloperskiego, jako alternatywę dla korzystania z Azure Portal.">Azure PowerShell</abbr> w przypadku tworzenia zasobów platformy Azure:

    + [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2,4 lub nowszej.

    + [Azure PowerShell](/powershell/azure/install-az-ps) w wersji 5,0 lub nowszej.

---

### <a name="2-verify-prerequisites"></a>2. Sprawdź wymagania wstępne

Sprawdź wymagania wstępne, które zależą od tego, czy używasz interfejsu wiersza polecenia platformy Azure, czy Azure PowerShell do tworzenia zasobów platformy Azure:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

+ W terminalu lub oknie poleceń Uruchom polecenie, `func --version` Aby sprawdzić, czy <abbr title="Zestaw narzędzi wiersza polecenia do pracy z Azure Functions na komputerze lokalnym.">Azure Functions Core Tools</abbr> są w wersji 3. x.

+ **Uruchom** `az --version` Aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure to 2,4 lub nowszej.

+ **Uruchom** `az login` Aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ **Uruchom** `dotnet --list-sdks` Aby sprawdzić, czy zainstalowano zestaw .NET Core SDK w wersji 3.1. x

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Uruchom** `func --version` Aby sprawdzić, czy Azure Functions Core Tools jest w wersji 3. x.

+ **Uruchom** `(Get-Module -ListAvailable Az).Version` i sprawdź wersję 5,0 lub nowszą. 

+ **Uruchom** `Connect-AzAccount` Aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

+ **Uruchom** `dotnet --list-sdks` Aby sprawdzić, czy zainstalowano zestaw .NET Core SDK w wersji 3.1. x

---

## <a name="3-create-a-local-function-project"></a>3. Utwórz projekt funkcji lokalnej

W tej sekcji utworzysz lokalną <abbr title="Kontener logiczny dla jednej lub kilku funkcji, które mogą być wdrażane i zarządzane wspólnie.">Projekt Azure Functions</abbr> w języku C#. Każda funkcja w projekcie odpowiada określonej <abbr title="Zdarzenie, które wywołuje kod funkcji, taki jak żądanie HTTP, komunikat kolejki lub określony czas.">uruchamiać</abbr>.

1. Uruchom `func init` polecenie, aby utworzyć projekt Functions w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Uruchom** "CD LocalFunctionProj", aby przejść do <abbr title="Ten folder zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie local.settings.jsi host.jsna. Ponieważ local.settings.json może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku. gitignore.">Folder projektu</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Dodaj funkcję do projektu za pomocą następującego polecenia:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    `--name`Argument jest unikatową nazwą funkcji (HttpExample).

    `--template`Argument określa wyzwalacz funkcji (http).

    
    <br/>   
    <details>  
    <summary><strong>Opcjonalne: kod dla HttpExample.cs</strong></summary>  
    
    *HttpExample.cs* zawiera `Run` metodę, która odbiera dane żądania w `req` zmiennej, jest identyfikatorem [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , który jest uzupełniony o **HttpTriggerAttribute**, który definiuje zachowanie wyzwalacza.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    Zwracany obiekt to element [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , który zwraca komunikat odpowiedzi jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) lub [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](./functions-bindings-http-webhook.md?tabs=csharp).  
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


1. Wybierz  <kbd>kombinację klawiszy CTRL + C</kbd> i wybierz pozycję <kbd>y</kbd> , aby zatrzymać host funkcji.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Utwórz obsługę zasobów platformy Azure dla swojej funkcji

Aby można było wdrożyć kod funkcji na platformie Azure, należy utworzyć <abbr title="Logiczny kontener dla powiązanych zasobów platformy Azure, którymi można zarządzać jako jednostką.">grupa zasobów</abbr>, a <abbr title="Konto, które zawiera wszystkie obiekty danych usługi Azure Storage. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych magazynu.">konto magazynu</abbr>i a <abbr title="Zasób w chmurze, który obsługuje funkcje bezserwerowe na platformie Azure, która zapewnia podstawowe środowisko obliczeniowe, w którym uruchamiane są funkcje.">Aplikacja funkcji</abbr> za pomocą następujących poleceń:

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


    ---

    Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` przy użyciu aplikacji funkcji systemu Windows lub aplikacji sieci Web, musisz użyć innej grupy zasobów.
    
1. Utwórz konto usługi Azure Storage ogólnego przeznaczenia w swojej grupie zasobów i regionie:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Zamień na `<STORAGE_NAME>` nazwę, która jest odpowiednia dla Ciebie i <abbr title="Nazwa musi być unikatowa dla wszystkich kont magazynu używanych przez wszystkich klientów platformy Azure globalnie. Można na przykład użyć kombinacji nazwy prywatnej lub firmowej, nazwy aplikacji i identyfikatora numerycznego, jak w contosobizappstorage20">unikatowa w usłudze Azure Storage</abbr>. Nazwy muszą zawierać od 3 do 24 znaków cyfry i małe litery. `Standard_LRS` Określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).


1. Utwórz aplikację funkcji na platformie Azure.
**Zamień** "<STORAGE_NAME> * * z nazwą w poprzednim kroku.
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
    
    Zamień `<STORAGE_NAME>` na nazwę konta użytego w poprzednim kroku.

    Zamień `<APP_NAME>` na <abbr title="Nazwa, która musi być unikatowa dla wszystkich klientów platformy Azure globalnie. Można na przykład użyć kombinacji nazwy prywatnej lub organizacji, nazwy aplikacji i identyfikatora liczbowego, tak jak w contoso-bizapp-Func-20.">Unikatowa nazwa</abbr>. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 

    <br/>
    <details>
    <summary><strong>Jaki jest koszt zasobów zainicjowanych na platformie Azure?</strong></summary>

    To polecenie tworzy aplikację funkcji działającą w określonym środowisku uruchomieniowym języka zgodnie z [planem zużycia Azure Functions](consumption-plan.md), który jest bezpłatny dla nakładu pracy w tym miejscu. Polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą którego można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie wiąże się z żadnymi kosztami, dopóki nie zostanie uaktywnione.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Wdróż projekt funkcji na platformie Azure


**Kopiuj** "Func Azure funtionapp Publish <APP_NAME> w terminalu **Zastąp** `<APP_NAME>` nazwą swojej aplikacji.
**Uruchom**

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

Skopiuj pełny **adres URL wywołania** pokazywany w danych wyjściowych `publish` polecenia na pasku adresu przeglądarki. **Dołącz** parametr zapytania **&Name = Functions**. 

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Wyczyść zasoby

Jeśli chcesz przejść do [następnego kroku](#next-steps) i dodać dane wyjściowe kolejki usługi Azure Storage <abbr title="Deklaratywne połączenie między funkcją i innymi zasobami. Powiązanie danych wejściowych zawiera dane do funkcji; powiązanie danych wyjściowych udostępnia dane z funkcji do innych zasobów.">powiązanie</abbr>Zachowaj wszystkie Twoje zasoby w miarę kompilowania już wykonanej pracy.

W przeciwnym razie użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

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
