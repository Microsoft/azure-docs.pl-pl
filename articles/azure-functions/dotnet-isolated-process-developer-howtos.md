---
title: Opracowywanie i publikowanie funkcji .NET 5 przy użyciu Azure Functions
description: Dowiedz się, jak tworzyć i debugować funkcje języka C# przy użyciu programu .NET 5.0, a następnie wdrażać lokalny projekt do hostingu bez serwera w Azure Functions.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: c76fde9a61ca60171ac094ef541e8c5841846aab
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866274"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Opracowywanie i publikowanie funkcji .NET 5 przy użyciu Azure Functions 

W tym artykule pokazano, jak pracować z funkcjami języka C# przy użyciu programu .NET 5.0, które są uruchamiane poza procesem z Azure Functions uruchomieniowego. Dowiesz się, jak tworzyć, debugować lokalnie i publikować te funkcje procesów izolowanych platformy .NET na platformie Azure. Na platformie Azure te funkcje działają w izolowanym procesie, który obsługuje platformę .NET 5.0. Aby dowiedzieć się więcej, zobacz [Przewodnik dotyczący uruchamiania funkcji na platformie .NET 5.0 na platformie Azure.](dotnet-isolated-process-guide.md)

Jeśli nie musisz obsługiwać programu .NET 5.0 lub uruchamiać funkcji poza procesem, możesz zamiast tego utworzyć funkcję biblioteki klas [języka C#.](functions-create-your-first-function-visual-studio.md)

>[!NOTE]
>Opracowywanie funkcji procesów izolowanych .NET w Azure Portal nie jest obecnie obsługiwane. Aby utworzyć aplikację funkcji na platformie Azure, która obsługuje uruchamianie aplikacji platformy .NET 5.0 poza procesem, należy użyć interfejsu wiersza polecenia platformy Azure lub usługi publikowania Visual Studio Code platformy Azure.   

## <a name="prerequisites"></a>Wymagania wstępne

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [Zestaw SDK platformy .NET 5.0](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) wersji 3.0.3381 lub nowszej.

+ [Interfejs wiersza polecenia](/cli/azure/install-azure-cli) platformy Azure w wersji 2.20 lub nowszej.  
::: zone pivot="development-environment-vscode"
+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ [Rozszerzenie obsługujące język C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) dla programu Visual Studio Code.  

+ Rozszerzenie [Azure Functions dla](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Visual Studio Code w wersji 1.3.0 lub nowszej.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019 r., w tym](https://azure.microsoft.com/downloads/)obciążenie tworzenie **aplikacji na platformie Azure.**  
Szablony projektów funkcji izolowanych .NET i publikowanie nie są obecnie dostępne w Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Tworzenie lokalnego projektu funkcji

W Azure Functions projekt funkcji jest kontenerem dla co najmniej jednej funkcji, która odpowiada na określony wyzwalacz. Wszystkie funkcje w projekcie mają te same konfiguracje lokalne i konfiguracje hostingu. W tej sekcji utworzysz projekt funkcji, który zawiera jedną funkcję.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Obecnie nie ma żadnych szablonów Visual Studio, które obsługują tworzenie izolowanych projektów funkcji .NET. W tym artykule pokazano, jak używać narzędzi Core Tools do tworzenia projektu języka C#, który można następnie uruchamiać lokalnie i debugować w Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Wybierz ikonę platformy Azure na pasku działań, a następnie w obszarze **Azure: Funkcje** wybierz ikonę **Utwórz nowy projekt...**.

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `C#`.

    + **Wybierz środowisko uruchomieniowe .NET:** wybierz pozycję `.NET 5 isolated` .

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz plik `HTTP trigger`.

    + **Podaj nazwę funkcji**: Wpisz `HttpExample`.

    + **Podaj przestrzeń nazw**: Wpisz `My.Functions`.

    + **Poziom autoryzacji:** wybierz `Anonymous` pozycję , która umożliwia każdemu wywołanie punktu końcowego funkcji. Aby dowiedzieć się więcej na temat poziomu autoryzacji, zobacz [Klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z wyzwalaczem HTTP. Lokalne pliki projektu można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej o utworzonych plikach, zobacz [Generated project files (Wygenerowane pliki projektu).](functions-develop-vs-code.md#generated-project-files)
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Uruchom `func init` polecenie w następujący sposób, aby utworzyć projekt funkcji w folderze o nazwie *LocalFunctionProj:*  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Określenie powoduje `dotnetisolated` utworzenie projektu, który działa na platformie .NET 5.0.


1. Przejdź do folderu projektu:

    ```console
    cd LocalFunctionProj
    ```

    Ten folder zawiera różne pliki dla projektu, w tymlocal.settings.js[ i](functions-run-local.md#local-settings-file)host.js[ plików](functions-host-json.md) konfiguracji. Ponieważ *local.settings.jsmoże zawierać* wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *.gitignore.*

1. Dodaj funkcję do projektu przy użyciu następującego polecenia, gdzie argument jest unikatową nazwą funkcji (HttpExample), a argument określa wyzwalacz `--name` `--template` funkcji (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` Tworzy plik kodu HttpExample.cs.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

W tym momencie możesz uruchomić polecenie z katalogu głównego folderu projektu, aby skompilować i uruchomić projekt `func start` funkcji izolowanych w języku C#. Obecnie jeśli chcesz debugować kod funkcji poza procesem w programie Visual Studio, musisz ręcznie dołączyć debuger do uruchomionego procesu środowiska uruchomieniowego usługi Functions, korzystając z następujących kroków:  

1. Otwórz plik projektu (csproj) w Visual Studio. Możesz przeglądać i modyfikować kod projektu oraz ustawiać w kodzie dowolne żądane punkty przerwania. 

1. W folderze projektu głównego użyj następującego polecenia z terminalu lub wiersza polecenia, aby uruchomić hosta środowiska uruchomieniowego:

    ```console
    func start --dotnet-isolated-debug
    ```

    Opcja `--dotnet-isolated-debug` nakazuje procesowi oczekiwanie na dołączenie debugera przed kontynuowaniem. Pod koniec danych wyjściowych powinny zostać wyświetlonych następujące wiersze: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    Wskazuje `PID: XXXXXX` identyfikator procesu (PID) procesu dotnet.exe, który jest uruchomionym hostem funkcji.
 
1. W Azure Functions danych wyjściowych środowiska uruchomieniowego zanotuj identyfikator procesu procesu hosta, do którego zostanie dołączyć debuger. Zanotuj również adres URL funkcji lokalnej.

1. W menu **Debugowanie** w Visual Studio pozycję Dołącz do **procesu...,** znajdź proces, który odpowiada identyfikatorowi procesu, a następnie wybierz **pozycję Dołącz**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Dołączanie debugera do procesu hosta usługi Functions":::    

    Po dołączeniu debugera można debugować kod funkcji w zwykły sposób.

1. Na pasku adresu przeglądarki wpisz adres URL funkcji lokalnej, który wygląda podobnie do poniższego, i uruchom żądanie. 

    `http://localhost:7071/api/HttpExample`

    Powinny zostać wyświetlony dane wyjściowe śledzenia z żądania zapisanego w uruchomionym terminalu. Wykonywanie kodu zatrzymuje się we wszystkich punktach przerwania ustawionych w kodzie funkcji.

1. Gdy wszystko będzie gotowe, przejdź do terminalu i naciśnij klawisze Ctrl +C, aby zatrzymać proces hosta.
 
Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

> [!NOTE]  
> Visual Studio publikowania nie jest obecnie dostępne dla aplikacji procesów izolowanych .NET. Po zakończeniu tworzenia projektu w usłudze Visual Studio do tworzenia zdalnych zasobów platformy Azure należy użyć interfejsu wiersza polecenia platformy Azure. Następnie możesz ponownie użyć polecenia Azure Functions Core Tools wiersza polecenia, aby opublikować projekt na platformie Azure.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie zasobów obsługi platformy Azure dla funkcji

Przed wdrożeniem kodu funkcji na platformie Azure należy utworzyć trzy zasoby:

- Grupa [zasobów](../azure-resource-manager/management/overview.md), która jest kontenerem logicznym dla powiązanych zasobów.
- Konto [magazynu](../storage/common/storage-account-create.md), które służy do obsługi stanu i innych informacji o funkcjach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji mapuje do lokalnego projektu funkcji i umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania i udostępniania zasobów.

Użyj następujących poleceń, aby utworzyć te elementy.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do platformy Azure:

    ```azurecli
    az login
    ```

    Polecenie [az login](/cli/azure/reference-index#az_login) loguje Cię do konta platformy Azure.

1. Utwórz grupę zasobów o `AzureFunctionsQuickstart-rg` nazwie w `westeurope` regionie:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Polecenie [az group create](/cli/azure/group#az_group_create) tworzy grupę zasobów. Zazwyczaj grupę zasobów i zasoby tworzy się w regionie w pobliżu, używając dostępnego regionu zwróconego z `az account list-locations` polecenia .

1. Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Polecenie [az storage account create](/cli/azure/storage/account#az_storage_account_create) tworzy konto magazynu. 

    W poprzednim przykładzie zastąp nazwą, która jest odpowiednia dla Ciebie i `<STORAGE_NAME>` unikatowa w usłudze Azure Storage. Nazwy muszą zawierać tylko cyfry od trzech do 24 znaków i małe litery. `Standard_LRS` Określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).

1. Utwórz aplikację funkcji na platformie Azure:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Polecenie [az functionapp create](/cli/azure/functionapp#az_functionapp_create) tworzy aplikację funkcji na platformie Azure. 
    
    W poprzednim przykładzie zastąp nazwą konta użytego w poprzednim kroku, a zastąp wartością globalnie unikatową `<STORAGE_NAME>` `<APP_NAME>` nazwą odpowiednią dla Ciebie. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
    
    To polecenie tworzy aplikację funkcji z programem .NET 5.0 w [ramach planu Azure Functions Zużycie.](consumption-plan.md) Ten plan powinien być bezpłatny w przypadku ilości użycia naliczonej w tym artykule. Polecenie apowiuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów. To wystąpienie umożliwia monitorowanie aplikacji funkcji i wyświetlanie dzienników. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie poniesie żadnych kosztów, dopóki nie zostanie aktywowane.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w subskrypcji platformy Azure, a następnie wdrożysz kod.

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.


1. Wybierz ikonę Azure na pasku działań, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wd wdrażaj do aplikacji funkcji....**

    ![Publikowanie projektu na platformie Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje po wyświetleniu monitów:

    - **Wybierz folder:** wybierz folder z obszaru roboczego lub przejdź do folderu zawierającego aplikację funkcji. Ten monit nie będzie wyświetlany, gdy masz już otwartą prawidłową aplikację funkcji.

    - **Wybierz subskrypcję:** wybierz subskrypcję do użycia. Ten monit nie będzie wyświetlany, jeśli masz tylko jedną subskrypcję.

    - **Wybierz aplikację funkcji na platformie Azure**: Wybierz plik `- Create new Function App`. (Nie wybieraj `Advanced` opcji, która nie jest objęta tym artykułem).
      
    - **Wprowadź globalnie unikatową nazwę aplikacji funkcji:** wpisz nazwę, która jest prawidłowa w ścieżce adresu URL. Wpisana nazwa jest weryfikowana, aby upewnić się, że jest unikatowa w Azure Functions.
    
    - **Wybierz stos środowiska uruchomieniowego:** wybierz pozycję `.NET 5 (non-LTS)` . 
    
    - **Wybierz lokalizację dla nowych zasobów:** aby uzyskać lepszą wydajność, [wybierz region](https://azure.microsoft.com/regions/) w pobliżu. 
    
    W obszarze powiadomień zobaczysz stan poszczególnych zasobów podczas ich tworzenia na platformie Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Powiadomienie o utworzeniu zasobu platformy Azure":::
    
1.  Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji przy użyciu nazw opartych na nazwie aplikacji funkcji:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Wybierz **pozycję Wyświetl dane** wyjściowe w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure. Jeśli pominiesz powiadomienie, wybierz ikonę dzwonka w prawym dolnym rogu, aby zobaczyć ją ponownie.

    ![Tworzenie kompletnego powiadomienia](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

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
Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć dodatkowych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Aby usunąć aplikację funkcji i powiązane z nią zasoby, aby uniknąć ponoszenia dodatkowych kosztów, należy wykonać poniższe kroki.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Aby usunąć aplikację funkcji i powiązane z nią zasoby, aby uniknąć ponoszenia dodatkowych kosztów, należy wykonać poniższe kroki.

1. W Eksploratorze chmury rozwiń swoją subskrypcję > **App Services**, kliknij prawym przyciskiem myszy aplikację funkcji i wybierz polecenie **Otwórz w portalu.** 

1. Na stronie aplikacji funkcji wybierz **kartę Przegląd,** a następnie wybierz link w obszarze **Grupa zasobów.**

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Wybieranie grupy zasobów do usunięcia ze strony aplikacji funkcji":::

2. Na **stronie Grupa zasobów** przejrzyj listę uwzględnionych zasobów i sprawdź, czy chcesz je usunąć.
 
3. Wybierz pozycję **Usuń grupę zasobów**, a następnie postępuj zgodnie z instrukcjami.

   Usuwanie może potrwać kilka minut. Po jego zakończeniu przez kilka sekund będzie widoczne powiadomienie. Możesz również wybrać ikonę dzwonka w górnej części strony, aby wyświetlić powiadomienie.
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o funkcjach izolowanych .NET](dotnet-isolated-process-guide.md)

