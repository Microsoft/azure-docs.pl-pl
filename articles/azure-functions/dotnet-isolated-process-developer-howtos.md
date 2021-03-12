---
title: Opracowywanie i publikowanie funkcji platformy .NET 5 przy użyciu Azure Functions
description: Dowiedz się, jak tworzyć i debugować funkcje języka C# przy użyciu programu .NET 5,0, a następnie wdrażać projekt lokalny do hostingu bezserwerowego w Azure Functions.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 9751d5d354d738a64d6b02e8153464491d78ec14
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201367"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Opracowywanie i publikowanie funkcji platformy .NET 5 przy użyciu Azure Functions 

W tym artykule pokazano, jak pracować z funkcjami języka C# przy użyciu programu .NET 5,0, który uruchamia się poza procesem w środowisku uruchomieniowym Azure Functions. Dowiesz się, jak tworzyć, debugować lokalnie i publikować te funkcje procesów izolowanych .NET na platformie Azure. Na platformie Azure te funkcje są uruchamiane w procesie izolowanym, który obsługuje platformę .NET 5,0. Aby dowiedzieć się więcej, zobacz [Przewodnik dotyczący uruchamiania funkcji na platformie .net 5,0 na platformie Azure](dotnet-isolated-process-guide.md).

Jeśli nie musisz obsługiwać programu .NET 5,0 ani uruchamiać funkcji poza procesem, możesz zamiast tego [utworzyć funkcję biblioteki klas języka C#](functions-create-your-first-function-visual-studio.md).

>[!NOTE]
>Tworzenie funkcji izolowanego procesu .NET w Azure Portal nie jest obecnie obsługiwane. Musisz użyć interfejsu wiersza polecenia platformy Azure lub publikowania Visual Studio Code, aby utworzyć aplikację funkcji na platformie Azure, która obsługuje uruchomione aplikacje .NET 5,0.   

## <a name="prerequisites"></a>Wymagania wstępne

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET SDK 5.0](https://www.microsoft.com/net/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) w wersji 3.0.3381 lub nowszej.

+ [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2,20 lub nowszej.  
::: zone pivot="development-environment-vscode"
+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ [Rozszerzenie obsługujące język C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) dla programu Visual Studio Code.  

+ [Rozszerzenie Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla Visual Studio Code, w wersji 1.3.0 lub nowszej.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Program Visual Studio 2019](https://azure.microsoft.com/downloads/), w tym obciążenie **Programowanie na platformie Azure** .  
Szablony i publikacje projektów funkcji izolowanych .NET nie są obecnie dostępne w programie Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Utwórz projekt funkcji lokalnej

W Azure Functions, projekt funkcji jest kontenerem dla jednej lub kilku poszczególnych funkcji, które reagują na konkretny wyzwalacz. Wszystkie funkcje w projekcie mają takie same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji zawierający pojedynczą funkcję.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> W tej chwili nie ma szablonów projektów programu Visual Studio, które obsługują tworzenie projektów funkcji izolowanych platformy .NET. W tym artykule przedstawiono sposób użycia podstawowych narzędzi do tworzenia projektu w języku C#, który można następnie uruchomić lokalnie i debugować w programie Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Wybierz ikonę platformy Azure na pasku działań, a następnie w obszarze **Azure: Funkcje** wybierz ikonę **Utwórz nowy projekt...**.

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `C#`.

    + **Wybierz środowisko uruchomieniowe platformy .NET**: Wybierz `.NET 5 isolated` .

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz plik `HTTP trigger`.

    + **Podaj nazwę funkcji**: Wpisz `HttpExample`.

    + **Podaj przestrzeń nazw**: Wpisz `My.Functions`.

    + **Poziom autoryzacji**: Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji. Aby dowiedzieć się więcej o poziomie autoryzacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z wyzwalaczem HTTP. Pliki projektu lokalnego można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej na temat tworzonych plików, zobacz [pliki wygenerowanego projektu](functions-develop-vs-code.md#generated-project-files).
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Uruchom `func init` polecenie w następujący sposób, aby utworzyć projekt Functions w folderze o nazwie *LocalFunctionProj*:  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Określenie `dotnetisolated` tworzenia projektu działającego w programie .net 5,0.


1. Przejdź do folderu projektu:

    ```console
    cd LocalFunctionProj
    ```

    Ten folder zawiera różne pliki dla projektu, w tym [local.settings.js](functions-run-local.md#local-settings-file) i [host.js](functions-host-json.md) plików konfiguracji. Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *. gitignore* .

1. Dodaj funkcję do projektu za pomocą następującego polecenia, gdzie `--name` argument jest unikatową nazwą funkcji (HttpExample), a `--template` argument określa wyzwalacz funkcji (http).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` tworzy plik kodu HttpExample.cs.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

W tym momencie można uruchomić `func start` polecenie z poziomu katalogu głównego folderu projektu, aby skompilować i uruchomić projekt funkcji izolowanych w języku C#. Obecnie Jeśli chcesz debugować kod funkcji poza procesem w programie Visual Studio, musisz ręcznie dołączyć debuger do działającego procesu środowiska uruchomieniowego funkcji, wykonując następujące czynności:  

1. Otwórz plik projektu (. csproj) w programie Visual Studio. Możesz przejrzeć i zmodyfikować kod projektu i ustawić wszystkie żądane punkty przerwania w kodzie. 

1. W folderze głównym projektu Użyj następującego polecenia w terminalu lub wierszu polecenia, aby uruchomić hosta środowiska uruchomieniowego:

    ```console
    func start --dotnet-isolated-debug
    ```

    `--dotnet-isolated-debug`Opcja instruuje proces, aby poczekać na dołączenie debugera przed kontynuowaniem. Na końcu danych wyjściowych powinna wyglądać podobnie do następujących wierszy: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    `PID: XXXXXX`Wskazuje identyfikator procesu (PID) procesu dotnet.exe, który jest hostem uruchomionych funkcji.
 
1. W danych wyjściowych środowiska uruchomieniowego Azure Functions Zanotuj identyfikator procesu hosta, do którego zostanie dołączony debuger. Należy również zwrócić uwagę na adres URL funkcji lokalnej.

1. Z menu **Debuguj** w programie Visual Studio wybierz pozycję **Dołącz do procesu...**, Znajdź proces zgodny z identyfikatorem procesu, a następnie wybierz pozycję **Dołącz**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Dołącz debuger do procesu hosta funkcji":::    

    Po dołączeniu debugera można debugować kod funkcji jako normalny.

1. Na pasku adresu przeglądarki wpisz adres URL funkcji lokalnej, który wygląda podobnie do poniższego, i uruchom żądanie. 

    <http://localhost:7071/api/HttpExample>

    Powinny być widoczne dane wyjściowe śledzenia z żądania zarejestrowanego w uruchomionym terminalu. Wykonanie kodu zostaje zatrzymane w dowolnym punkcie przerwania ustawionym w kodzie funkcji.

1. Gdy skończysz, przejdź do terminalu i naciśnij kombinację klawiszy CTRL + C, aby zatrzymać proces hosta.
 
Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

> [!NOTE]  
> Publikacja programu Visual Studio nie jest obecnie dostępna dla aplikacji procesów izolowanych platformy .NET. Po zakończeniu opracowywania projektu w programie Visual Studio należy utworzyć zdalne zasoby platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Następnie możesz ponownie użyć Azure Functions Core Tools z wiersza polecenia, aby opublikować projekt na platformie Azure.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie obsługi zasobów platformy Azure dla funkcji

Aby można było wdrożyć kod funkcji na platformie Azure, należy utworzyć trzy zasoby:

- [Grupa zasobów](../azure-resource-manager/management/overview.md), która jest kontenerem logicznym dla powiązanych zasobów.
- [Konto magazynu](../storage/common/storage-account-create.md), które jest używane do przechowywania stanu i innych informacji o funkcjach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji jest mapowana na projekt funkcji lokalnej i umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów.

Użyj następujących poleceń, aby utworzyć te elementy.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do platformy Azure:

    ```azurecli
    az login
    ```

    Polecenie [AZ login umożliwia zalogowanie](/cli/azure/reference-index#az_login) się do konta platformy Azure.

1. Utwórz grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` w `westeurope` regionie:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Polecenie [AZ Group Create](/cli/azure/group#az_group_create) tworzy grupę zasobów. Zazwyczaj tworzysz grupę zasobów i zasoby w regionie, w którym wiesz, przy użyciu dostępnego regionu zwróconego przez `az account list-locations` polecenie.

1. Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Polecenie [AZ Storage account Create](/cli/azure/storage/account#az_storage_account_create) tworzy konto magazynu. 

    W poprzednim przykładzie Zastąp `<STORAGE_NAME>` wartość nazwą, która jest odpowiednia dla Ciebie i unikatową w usłudze Azure Storage. Nazwy muszą zawierać od 3 do 24 znaków cyfry i małe litery. `Standard_LRS` Określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).

1. Tworzenie aplikacji funkcji na platformie Azure:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Polecenie [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) tworzy aplikację funkcji na platformie Azure. 
    
    W poprzednim przykładzie Zastąp ciąg `<STORAGE_NAME>` nazwą konta użytego w poprzednim kroku i Zastąp ciąg `<APP_NAME>` globalnie unikatową nazwą, która jest odpowiednia dla Ciebie. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
    
    To polecenie tworzy aplikację funkcji z programem .NET 5,0 w ramach [planu użycia Azure Functions](consumption-plan.md). Ten plan powinien być bezpłatny dla wielkości użytkowania poniesionych w tym artykule. Polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów. To wystąpienie służy do monitorowania aplikacji funkcji i wyświetlania dzienników. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie wiąże się z żadnymi kosztami, dopóki nie zostanie uaktywnione.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w ramach subskrypcji platformy Azure, a następnie wdrożono swój kod.

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.


1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje po wyświetleniu monitów:

    - **Wybierz folder**: Wybierz folder z obszaru roboczego lub przejdź do folderu, który zawiera aplikację funkcji. Ten monit nie zostanie wyświetlony, jeśli masz już otwartą prawidłową aplikację funkcji.

    - **Wybierz subskrypcję**: wybierz subskrypcję do użycia. Ten monit nie zostanie wyświetlony, jeśli masz tylko jedną subskrypcję.

    - **Wybierz aplikację funkcji na platformie Azure**: Wybierz plik `- Create new Function App`. (Nie należy wybierać `Advanced` opcji, która nie jest objęta tym artykułem).
      
    - **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest prawidłowa w ścieżce URL. Wpisana nazwa zostanie sprawdzona, aby upewnić się, że jest ona unikatowa w Azure Functions.
    
    - **Wybierz stos środowiska uruchomieniowego**: Wybierz `.NET 5 (non-LTS)` . 
    
    - **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie. 
    
    W obszarze powiadomień zobaczysz stan poszczególnych zasobów w miarę ich tworzenia na platformie Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Powiadomienie o tworzeniu zasobów platformy Azure":::
    
1.  Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji przy użyciu nazw opartych na nazwie aplikacji funkcji:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure. W przypadku odrzucenia powiadomienia wybierz ikonę dzwonka w prawym dolnym rogu, aby zobaczyć ją ponownie.

    ![Utwórz pełne powiadomienie](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby zostały utworzone w celu ukończenia tego artykułu. Za te zasoby może zostać naliczona opłata — zależy to od Twojego [stanu konta](https://azure.microsoft.com/account/) i [cennika usług](https://azure.microsoft.com/pricing/). 

::: zone pivot="development-environment-cli"  
Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Wykonaj następujące kroki, aby usunąć aplikację funkcji i powiązane z nią zasoby, aby uniknąć ponoszenia dalszych kosztów.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Wykonaj następujące kroki, aby usunąć aplikację funkcji i powiązane z nią zasoby, aby uniknąć ponoszenia dalszych kosztów.

1. W Eksploratorze chmury rozwiń **App Services**> subskrypcji, kliknij prawym przyciskiem myszy aplikację funkcji, a następnie wybierz polecenie **Otwórz w portalu**. 

1. Na stronie aplikacja funkcji wybierz kartę **Przegląd** , a następnie wybierz link w obszarze **Grupa zasobów**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Wybierz grupę zasobów do usunięcia ze strony aplikacji funkcji":::

2. Na stronie **Grupa zasobów** zapoznaj się z listą uwzględnionych zasobów i sprawdź, czy są one tymi, które chcesz usunąć.
 
3. Wybierz pozycję **Usuń grupę zasobów**, a następnie postępuj zgodnie z instrukcjami.

   Usuwanie może potrwać kilka minut. Po jego zakończeniu przez kilka sekund będzie widoczne powiadomienie. Możesz również wybrać ikonę dzwonka w górnej części strony, aby wyświetlić powiadomienie.
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o funkcjach izolowanych platformy .NET](dotnet-isolated-process-guide.md)

