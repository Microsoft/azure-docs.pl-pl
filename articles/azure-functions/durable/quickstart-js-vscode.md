---
title: Tworzenie pierwszej funkcji trwałej na platformie Azure przy użyciu języka JavaScript
description: Utwórz i Opublikuj funkcję trwałej platformy Azure w języku JavaScript przy użyciu Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 05/07/2020
ms.reviewer: azfuncdf, antchu
ms.custom: devx-track-js
ms.openlocfilehash: f8ffa90ba0f1ac32d4691165fabf3d8eb9fb7605
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91335453"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Tworzenie pierwszej funkcji trwałej w języku Java Script

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

W tym artykule przedstawiono użycie rozszerzenia programu Visual Studio Code dla usługi Azure Functions w celu lokalnego utworzenia i przetestowania funkcji trwałej „hello world”.  Ta funkcja będzie aranżować i łączyć w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure.

![Uruchamianie funkcji trwałej na platformie Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj [Visual Studio Code](https://code.visualstudio.com/download).

* Instalowanie rozszerzenia [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code

* Upewnij się, że masz najnowszą wersję [Azure Functions Core Tools](../functions-run-local.md).

* Durable Functions wymagać konta usługi Azure Storage. Potrzebna jest subskrypcja platformy Azure.

* Upewnij się, że masz zainstalowaną wersję 10. x lub 12. x [Node.js](https://nodejs.org/) .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny 

W tej sekcji użyto Visual Studio Code do utworzenia projektu Azure Functions lokalnego. 

1. W Visual Studio Code naciśnij klawisz F1 (lub Ctrl/Cmd + Shift + P), aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create New Project...` .

    ![Create, funkcja](media/quickstart-js-vscode/functions-create-project.png)

1. Wybierz pustą lokalizację folderu dla projektu i wybierz **pozycję Wybierz**.

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybierz język projektu aplikacji funkcji | JavaScript | Utwórz projekt lokalnych funkcji Node.js. |
    | Wybierz wersję | Azure Functions, wersja 3 | Ta opcja jest wyświetlana tylko wtedy, gdy podstawowe narzędzia nie są jeszcze zainstalowane. W takim przypadku podstawowe narzędzia są instalowane przy pierwszym uruchomieniu aplikacji. |
    | Wybierz szablon dla pierwszej funkcji projektu | Pomiń teraz | |
    | Wybierz, w jaki sposób chcesz otworzyć projekt | Otwórz w bieżącym oknie | Ponownie otwiera VS Code w wybranym folderze. |

Visual Studio Code instaluje Azure Functions Core Tools, w razie konieczności. Tworzy również projekt aplikacji funkcji w folderze. Ten projekt zawiera [host.js](../functions-host-json.md) i [local.settings.js](../functions-run-local.md#local-settings-file) plików konfiguracyjnych.

package.jsw pliku jest również tworzony w folderze głównym.

## <a name="install-the-durable-functions-npm-package"></a>Instalowanie pakietu npm rozszerzenia Durable Functions

Aby można było korzystać z Durable Functions w aplikacji funkcji Node.js, należy użyć biblioteki o nazwie `durable-functions` .

1. Użyj menu *Widok* lub Ctrl + Shift + ', aby otworzyć nowy terminal w vs Code.

1. Zainstaluj pakiet npm `durable-functions`, uruchamiając polecenie `npm install durable-functions` w katalogu głównym aplikacji funkcji.

## <a name="creating-your-functions"></a>Tworzenie funkcji

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
    | Wybieranie szablonu dla funkcji | Durable Functions Orchestrator | Tworzenie Durable Functions aranżacji |
    | Podaj nazwę funkcji | HelloOrchestrator | Nazwa funkcji trwałej |

Dodano koordynatora do funkcji działania współrzędnych. Otwórz *HelloOrchestrator/index.js* , aby wyświetlić funkcję programu Orchestrator. Każde wywołanie `context.df.callActivity` wywołuje funkcję działania o nazwie `Hello` .

Następnie dodasz funkcję działania, której dotyczy odwołanie `Hello` .

### <a name="activity-function"></a>Activity — funkcja

1. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create Function...` .

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Działanie Durable Functions | Tworzenie funkcji działania |
    | Podaj nazwę funkcji | Hello | Nazwa funkcji działania |

Dodano `Hello` funkcję działania, która jest wywoływana przez program Orchestrator. Otwórz *powitanie/index.js* , aby zobaczyć, że przyjmujemy nazwę jako dane wejściowe i zwracają powitanie. Funkcja działania polega na tym, że wykonujesz akcje, takie jak wykonywanie wywołania bazy danych lub wykonywanie obliczeń.

Na koniec dodasz funkcję wyzwalaną przez protokół HTTP, która uruchamia aranżację.

### <a name="client-function-http-starter"></a>Funkcja klienta (HTTP Starter)

1. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create Function...` .

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Durable Functions HTTP Starter | Tworzenie funkcji HTTP Starter |
    | Podaj nazwę funkcji | DurableFunctionsHttpStart | Nazwa funkcji działania |
    | Poziom autoryzacji | Anonimowe | W celach demonstracyjnych Zezwól funkcji na wywoływanie bez uwierzytelniania |

Dodano funkcję wyzwalaną przez protokół HTTP, która uruchamia aranżację. Otwórz *DurableFunctionsHttpStart/index.js* , aby zobaczyć, że używa on `client.startNew` do uruchomienia nowej aranżacji. Następnie używa `client.createCheckStatusResponse` do zwrócenia odpowiedzi HTTP zawierającej adresy URL, których można użyć do monitorowania nowej aranżacji i zarządzania nią.

Masz teraz aplikację Durable Functions, którą można uruchomić lokalnie i wdrożyć na platformie Azure.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio Code.

1. Aby przetestować funkcję, należy ustawić punkt przerwania w `Hello` kodzie funkcji działania (*Hello/index.js*). Naciśnij klawisz F5 lub wybierz `Debug: Start Debugging` z palety poleceń, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

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

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. Skopiuj adres URL wyzwalacza HTTP z panelu **Dane wyjściowe**. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Podczas korzystania z opublikowanej aplikacji powinna zostać zwrócona taka sama odpowiedź dotycząca stanu.

## <a name="next-steps"></a>Następne kroki

Utworzono i opublikowano aplikację funkcji trwałej w języku JavaScript za pomocą narzędzia Visual Studio Code.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md#application-patterns)
