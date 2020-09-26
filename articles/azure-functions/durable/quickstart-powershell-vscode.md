---
title: Tworzenie pierwszej trwałej funkcji w Azure Functions przy użyciu programu PowerShell
description: Utwórz i Opublikuj funkcję trwałej platformy Azure w programie PowerShell przy użyciu Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 08/10/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: edd02085abe63b124082255247362f096248ba82
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317263"
---
# <a name="create-your-first-durable-function-in-powershell"></a>Tworzenie pierwszej trwałej funkcji w programie PowerShell

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

W tym artykule przedstawiono użycie rozszerzenia programu Visual Studio Code dla usługi Azure Functions w celu lokalnego utworzenia i przetestowania funkcji trwałej „hello world”.  Ta funkcja będzie aranżować i łączyć w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure.

![Uruchamianie funkcji trwałej na platformie Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj [Visual Studio Code](https://code.visualstudio.com/download).

* Instalowanie rozszerzenia [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code

* Upewnij się, że masz najnowszą wersję [Azure Functions Core Tools](../functions-run-local.md).

* Durable Functions wymagać konta usługi Azure Storage. Potrzebna jest subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny 

W tej sekcji użyto Visual Studio Code do utworzenia projektu Azure Functions lokalnego. 

1. W Visual Studio Code naciśnij klawisz F1 (lub Ctrl/Cmd + Shift + P), aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create New Project...` .

    ![Tworzenie funkcji](media/quickstart-js-vscode/functions-create-project.png)

1. Wybierz pustą lokalizację folderu dla projektu i wybierz **pozycję Wybierz**.

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybierz język projektu aplikacji funkcji | PowerShell | Utwórz projekt funkcji lokalnych programu PowerShell. |
    | Wybierz wersję | Azure Functions, wersja 3 | Ta opcja jest wyświetlana tylko wtedy, gdy podstawowe narzędzia nie są jeszcze zainstalowane. W takim przypadku podstawowe narzędzia są instalowane przy pierwszym uruchomieniu aplikacji. |
    | Wybierz szablon dla pierwszej funkcji projektu | Pomiń teraz | |
    | Wybierz, w jaki sposób chcesz otworzyć projekt | Otwórz w bieżącym oknie | Ponownie otwiera VS Code w wybranym folderze. |

Visual Studio Code instaluje Azure Functions Core Tools, w razie konieczności. Tworzy również projekt aplikacji funkcji w folderze. Ten projekt zawiera [host.js](../functions-host-json.md) i [local.settings.js](../functions-run-local.md#local-settings-file) plików konfiguracyjnych.

package.jsw pliku jest również tworzony w folderze głównym.

### <a name="configure-function-app-to-use-powershell-7"></a>Konfigurowanie aplikacji funkcji do korzystania z programu PowerShell 7

Otwórz *local.settings.jsw* pliku i upewnij się, że ustawienie o nazwie `FUNCTIONS_WORKER_RUNTIME_VERSION` jest ustawione na `~7` . Jeśli brakuje lub ustawiono inną wartość, zaktualizuj zawartość pliku.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

## <a name="create-your-functions"></a>Tworzenie funkcji

Najbardziej podstawowa aplikacja Durable Functions zawiera trzy funkcje:

* *Funkcja programu Orchestrator* — opisuje przepływ pracy, który organizuje inne funkcje.
* *Funkcja działania* — wywołana przez funkcję programu Orchestrator, wykonuje pracę i opcjonalnie zwraca wartość.
* *Funkcja klienta* — zwykła funkcja platformy Azure, która uruchamia funkcję programu Orchestrator. Ten przykład używa funkcji wyzwalanej przez protokół HTTP.

### <a name="orchestrator-function"></a>Funkcja programu Orchestrator

Za pomocą szablonu można utworzyć trwały kod funkcji w projekcie.

1. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create Function...` .

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Durable Functions Orchestrator (wersja zapoznawcza) | Tworzenie Durable Functions aranżacji |
    | Podaj nazwę funkcji | HelloOrchestrator | Nazwa funkcji trwałej |

Dodano koordynatora do funkcji działania współrzędnych. Otwórz *HelloOrchestrator/run.ps1* , aby wyświetlić funkcję programu Orchestrator. Każde wywołanie `Invoke-ActivityFunction` polecenia cmdlet wywołuje funkcję działania o nazwie `Hello` .

Następnie dodasz funkcję działania, której dotyczy odwołanie `Hello` .

### <a name="activity-function"></a>Activity — funkcja

1. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create Function...` .

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Działanie Durable Functions (wersja zapoznawcza) | Tworzenie funkcji działania |
    | Podaj nazwę funkcji | Hello | Nazwa funkcji działania |

Dodano `Hello` funkcję działania, która jest wywoływana przez program Orchestrator. Otwórz *powitanie/run.ps1* , aby zobaczyć, że przyjmujemy nazwę jako dane wejściowe i zwracają powitanie. Funkcja działania polega na tym, że wykonujesz akcje, takie jak wykonywanie wywołania bazy danych lub wykonywanie obliczeń.

Na koniec dodasz funkcję wyzwalaną przez protokół HTTP, która uruchamia aranżację.

### <a name="client-function-http-starter"></a>Funkcja klienta (HTTP Starter)

1. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create Function...` .

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Durable Functions HTTP Starter (wersja zapoznawcza) | Tworzenie funkcji HTTP Starter |
    | Podaj nazwę funkcji | HttpStart | Nazwa funkcji działania |
    | Poziom autoryzacji | Anonimowe | W celach demonstracyjnych Zezwól funkcji na wywoływanie bez uwierzytelniania |

Dodano funkcję wyzwalaną przez protokół HTTP, która uruchamia aranżację. Otwórz *HttpStart/run.ps1* , aby zobaczyć, że używa `Start-NewOrchestration` polecenia cmdlet do uruchomienia nowej aranżacji. Następnie używa `New-OrchestrationCheckStatusResponse` polecenia cmdlet, aby zwrócić odpowiedź HTTP zawierającą adresy URL, których można użyć do monitorowania nowej aranżacji i zarządzania nią.

Masz teraz aplikację Durable Functions, którą można uruchomić lokalnie i wdrożyć na platformie Azure.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Po pierwszym uruchomieniu aplikacji funkcji z Visual Studio Code zostanie wyświetlony monit o zainstalowanie tych narzędzi.

1. Aby przetestować funkcję, należy ustawić punkt przerwania w `Hello` kodzie funkcji działania (*Hello/run.ps1*). Naciśnij klawisz F5 lub wybierz `Debug: Start Debugging` z palety poleceń, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat debugowania, zapoznaj się z [diagnostyką Durable Functions](durable-functions-diagnostics.md#debugging) .

1. Durable Functions wymaga uruchomienia konta usługi Azure Storage. Gdy VS Code zostanie wyświetlony komunikat z pytaniem o wybranie konta magazynu, wybierz **pozycję Wybierz konto magazynu**.

    ![Tworzenie konta magazynu](media/quickstart-js-vscode/functions-select-storage.png)

1. Postępując zgodnie z instrukcjami, podaj poniższe informacje, aby utworzyć nowe konto magazynu na platformie Azure.

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie subskrypcji | *Nazwa subskrypcji* | Wybierz subskrypcję platformy Azure |
    | Wybierz konto magazynu | Tworzenie nowego konta magazynu |  |
    | Wprowadź nazwę nowego konta magazynu | *Unikatowa nazwa* | Nazwa konta magazynu do utworzenia |
    | Wybieranie grupy zasobów | *Unikatowa nazwa* | Nazwa grupy zasobów do utworzenia |
    | Wybieranie lokalizacji | *okolicy* | Zaznacz region blisko siebie |

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](media/quickstart-js-vscode/functions-f5.png)

1. Za pomocą przeglądarki lub narzędzia, takiego jak [Poster](https://www.getpostman.com/) lub [zwinięcie](https://curl.haxx.se/), Wyślij żądanie HTTP POST do punktu końcowego adresu URL. Zastąp ostatni segment nazwą funkcji programu Orchestrator ( `HelloOrchestrator` ). Adres URL powinien być podobny do `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

   Odpowiedź to początkowy wynik z funkcji HTTP informującej o pomyślnym uruchomieniu trwałej aranżacji. Nie jest to jeszcze końcowy wynik aranżacji. Odpowiedź zawiera kilka przydatnych adresów URL. Na razie wykonajmy zapytanie o stan aranżacji.

1. Skopiuj wartość adresu URL `statusQueryGetUri` i wklej ją na pasku adresu przeglądarki i wykonaj żądanie. Możesz również nadal korzystać z programu Poster w celu wystawienia żądania GET.

   Żądanie wykona zapytanie o stan wystąpienia aranżacji. Powinna zostać wyświetlona ostateczna odpowiedź, która pokazuje, że wystąpienie zostało zakończone i zawiera dane wyjściowe lub wyniki funkcji trwałej. Wygląda na to: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Aby zatrzymać debugowanie, naciśnij klawisze **Shift + F5** w vs Code.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w ramach subskrypcji platformy Azure, a następnie wdrożono swój kod. 

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure. 


1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](../../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje na ekranie:

    + **Wybierz folder**: Wybierz folder z obszaru roboczego lub przejdź do folderu, który zawiera aplikację funkcji. Nie zobaczysz tego, jeśli masz już otwartą prawidłową aplikację funkcji.

    + **Wybierz subskrypcję**: wybierz subskrypcję do użycia. Nie zobaczysz tego, jeśli masz tylko jedną subskrypcję.

    + **Wybierz Aplikacja funkcji na platformie Azure**: wybierz opcję `+ Create new Function App` . (Nie należy wybierać `Advanced` opcji, która nie jest objęta tym artykułem).
      
    + **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest prawidłowa w ścieżce URL. Wpisana nazwa zostanie sprawdzona, aby upewnić się, że jest ona unikatowa w Azure Functions. 

    + **Wybierz środowisko uruchomieniowe**: Wybierz wersję programu PowerShell uruchomioną lokalnie. Możesz użyć polecenia, `pwsh -version` Aby sprawdzić wersję.

        > [!NOTE]
        > Rozszerzenie VS Code Azure Functions nie może jeszcze obsługiwać programu PowerShell 7. Jeśli program PowerShell 7 nie jest dostępny jako opcja, wybierz teraz opcję PowerShell 6. x i [zaktualizuj wersję ręcznie](#update-function-app-ps7) po utworzeniu aplikacji funkcji.

    + **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie. 
    
1.  Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji przy użyciu nazw opartych na nazwie aplikacji funkcji:
    
    + Grupa zasobów, która jest kontenerem logicznym dla powiązanych zasobów.
    + Standardowe konto usługi Azure Storage, które zachowuje stan i inne informacje o projektach.
    + Plan zużycia, który definiuje podstawowego hosta dla aplikacji funkcji bezserwerowej. 
    + Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów w ramach tego samego planu hostingu.
    + Wystąpienie Application Insights połączone z aplikacją funkcji, która śledzi użycie funkcji bezserwerowej.

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie.

1. <a name="update-function-app-ps7"></a>Jeśli nie możesz wybrać *programu PowerShell 7* wcześniej podczas tworzenia aplikacji funkcji, naciśnij klawisz F1 (lub Ctrl/Cmd + Shift + P), aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Upload Local Settings...` . Postępuj zgodnie z monitami, aby wybrać utworzoną aplikację funkcji. Jeśli zostanie wyświetlony monit o zastąpienie istniejących ustawień, wybierz pozycję *nie do wszystkich*.
    
1. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure. W przypadku odrzucenia powiadomienia wybierz ikonę dzwonka w prawym dolnym rogu, aby zobaczyć ją ponownie.

    ![Utwórz pełne powiadomienie](../../../includes/media/functions-publish-project-vscode/function-create-notifications.png)


## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. Skopiuj adres URL wyzwalacza HTTP z panelu **Dane wyjściowe**. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Podczas korzystania z opublikowanej aplikacji powinna zostać zwrócona taka sama odpowiedź dotycząca stanu.

## <a name="next-steps"></a>Następne kroki

Użyto Visual Studio Code, aby utworzyć i opublikować aplikację funkcji trwałej programu PowerShell.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md#application-patterns)
