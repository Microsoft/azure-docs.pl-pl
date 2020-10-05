---
title: Tworzenie pierwszej trwałej funkcji na platformie Azure przy użyciu języka Python
description: Utwórz i Opublikuj funkcję trwałej platformy Azure w języku Python przy użyciu Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 04/04/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 5d624027259212d804ced26a6daaffb853984a98
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91294879"
---
# <a name="create-your-first-durable-function-in-python"></a>Tworzenie pierwszej trwałej funkcji w języku Python

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

W tym artykule przedstawiono użycie rozszerzenia programu Visual Studio Code dla usługi Azure Functions w celu lokalnego utworzenia i przetestowania funkcji trwałej „hello world”.  Ta funkcja będzie aranżować i łączyć w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure.

![Uruchamianie funkcji trwałej na platformie Azure](./media/quickstart-python-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj [Visual Studio Code](https://code.visualstudio.com/download).

* Zainstaluj rozszerzenie VS Code [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) .

* Upewnij się, że masz najnowszą wersję [Azure Functions Core Tools](../functions-run-local.md).

* Durable Functions wymagać konta usługi Azure Storage. Potrzebna jest subskrypcja platformy Azure.

* Upewnij się, że masz zainstalowaną wersję 3,6, 3,7 lub 3,8 języka [Python](https://www.python.org/) .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny 

W tej sekcji użyto Visual Studio Code do utworzenia projektu Azure Functions lokalnego. 

1. W Visual Studio Code naciśnij klawisz F1 (lub Ctrl/Cmd + Shift + P), aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create New Project...` .

    ![Tworzenie funkcji](media/quickstart-python-vscode/functions-create-project.png)

1. Wybierz pustą lokalizację folderu dla projektu i wybierz **pozycję Wybierz**.

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybierz język projektu aplikacji funkcji | Python | Utwórz projekt lokalnych funkcji języka Python. |
    | Wybierz wersję | Azure Functions, wersja 3 | Ta opcja jest wyświetlana tylko wtedy, gdy podstawowe narzędzia nie są jeszcze zainstalowane. W takim przypadku podstawowe narzędzia są instalowane przy pierwszym uruchomieniu aplikacji. |
    | Wersja języka Python | Python 3,6, 3,7 lub 3,8 | VS Code utworzy środowisko wirtualne z wybraną wersją. |
    | Wybierz szablon dla pierwszej funkcji projektu | Pomiń teraz | |
    | Wybierz, w jaki sposób chcesz otworzyć projekt | Otwórz w bieżącym oknie | Ponownie otwiera VS Code w wybranym folderze. |

Visual Studio Code instaluje Azure Functions Core Tools, w razie konieczności. Tworzy również projekt aplikacji funkcji w folderze. Ten projekt zawiera [host.js](../functions-host-json.md) i [local.settings.js](../functions-run-local.md#local-settings-file) plików konfiguracyjnych.

Plik requirements.txt jest również tworzony w folderze głównym. Określa pakiety języka Python niezbędne do uruchomienia aplikacji funkcji.

## <a name="install-azure-functions-durable-from-pypi"></a>Instalowanie platformy Azure-Functions — trwałe z PyPI

Podczas tworzenia projektu rozszerzenie Azure Functions VS Code automatycznie utworzyło środowisko wirtualne z wybraną wersją języka Python. Środowisko wirtualne można aktywować w terminalu i zainstalować niektóre zależności wymagane przez Azure Functions i Durable Functions.

1. Otwórz `requirements.txt` w edytorze i zmień jego zawartość na następujące:

    ```
    azure-functions
    azure-functions-durable>=1.0.0b6
    ```

1. Otwórz zintegrowany terminal edytora w bieżącym folderze ( `` Ctrl-Shift-` `` ).

1. W zintegrowanym terminalu Aktywuj środowisko wirtualne w bieżącym folderze:

    **Linux lub macOS**

    ```bash
    source .venv/bin/activate
    ```

    **Windows**

    ```powershell
    .venv\scripts\activate
    ```

    ![Aktywuj środowisko wirtualne](media/quickstart-python-vscode/activate-venv.png)

1. W zintegrowanym terminalu, w którym środowisko wirtualne jest aktywowane, użyj narzędzia PIP, aby zainstalować właśnie zdefiniowane pakiety:

    ```bash
    python -m pip install -r requirements.txt
    ```

## <a name="create-your-functions"></a>Tworzenie funkcji

Podstawowa aplikacja Durable Functions zawiera trzy funkcje:

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

Dodano koordynatora do funkcji działania współrzędnych. Otwórz *HelloOrchestrator/ \_ \_ init__. PR* , aby wyświetlić funkcję programu Orchestrator. Każde wywołanie `context.call_activity` wywołuje funkcję działania o nazwie `Hello` .

Następnie dodasz funkcję działania, której dotyczy odwołanie `Hello` .

### <a name="activity-function"></a>Activity — funkcja

1. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create Function...` .

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Działanie Durable Functions | Tworzenie funkcji działania |
    | Podaj nazwę funkcji | Hello | Nazwa funkcji działania |

Dodano `Hello` funkcję działania, która jest wywoływana przez program Orchestrator. Otwórz plik *Hello/ \_ \_ init__. PR* , aby zobaczyć, że przyjmuje nazwę jako dane wejściowe i zwraca powitanie. Funkcja działania polega na tym, że wykonujesz akcje, takie jak wykonywanie wywołania bazy danych lub wykonywanie obliczeń.

Na koniec dodasz funkcję wyzwalaną przez protokół HTTP, która uruchamia aranżację.

### <a name="client-function-http-starter"></a>Funkcja klienta (HTTP Starter)

1. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create Function...` .

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Durable Functions HTTP Starter | Tworzenie funkcji HTTP Starter |
    | Podaj nazwę funkcji | DurableFunctionsHttpStart | Nazwa funkcji działania |
    | Poziom autoryzacji | Anonimowe | W celach demonstracyjnych Zezwól funkcji na wywoływanie bez uwierzytelniania |

Dodano funkcję wyzwalaną przez protokół HTTP, która uruchamia aranżację. Otwórz plik *DurableFunctionsHttpStart/ \_ \_ init__. PR* , aby zobaczyć, że jest on wykorzystywany `client.start_new` do uruchamiania nowej aranżacji. Następnie używa `client.create_check_status_response` do zwrócenia odpowiedzi HTTP zawierającej adresy URL, których można użyć do monitorowania nowej aranżacji i zarządzania nią.

Masz teraz aplikację Durable Functions, którą można uruchomić lokalnie i wdrożyć na platformie Azure.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Jeśli go nie zainstalowano, zostanie wyświetlony monit o zainstalowanie tych narzędzi przy pierwszym uruchomieniu funkcji z Visual Studio Code.

1. Aby przetestować funkcję, należy ustawić punkt przerwania w `Hello` kodzie funkcji działania (*Hello/ \_ \_ init__. PR*). Naciśnij klawisz F5 lub wybierz `Debug: Start Debugging` z palety poleceń, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat debugowania, zapoznaj się z [diagnostyką Durable Functions](durable-functions-diagnostics.md#debugging) .

1. Durable Functions wymaga uruchomienia konta usługi Azure Storage. Gdy VS Code zostanie wyświetlony komunikat z pytaniem o wybranie konta magazynu, wybierz **pozycję Wybierz konto magazynu**.

    ![Tworzenie konta magazynu](media/quickstart-python-vscode/functions-select-storage.png)

1. Postępując zgodnie z instrukcjami, podaj poniższe informacje, aby utworzyć nowe konto magazynu na platformie Azure.

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie subskrypcji | *Nazwa subskrypcji* | Wybierz subskrypcję platformy Azure |
    | Wybierz konto magazynu | Tworzenie nowego konta magazynu |  |
    | Wprowadź nazwę nowego konta magazynu | *Unikatowa nazwa* | Nazwa konta magazynu do utworzenia |
    | Wybieranie grupy zasobów | *Unikatowa nazwa* | Nazwa grupy zasobów do utworzenia |
    | Wybieranie lokalizacji | *region* | Zaznacz region blisko siebie |

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](media/quickstart-python-vscode/functions-f5.png)

1. Za pomocą przeglądarki lub narzędzia, takiego jak [Poster](https://www.getpostman.com/) lub [zwinięcie](https://curl.haxx.se/), Wyślij żądanie HTTP do punktu końcowego adresu URL. Zastąp ostatni segment nazwą funkcji programu Orchestrator ( `HelloOrchestrator` ). Adres URL powinien być podobny do `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

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

1. Skopiuj adres URL wyzwalacza HTTP z panelu **Dane wyjściowe**. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format: `http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Podczas korzystania z opublikowanej aplikacji powinna zostać zwrócona taka sama odpowiedź dotycząca stanu.

## <a name="next-steps"></a>Następne kroki

Użyto Visual Studio Code, aby utworzyć i opublikować aplikację funkcji trwałej języka Python.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md#application-patterns)
