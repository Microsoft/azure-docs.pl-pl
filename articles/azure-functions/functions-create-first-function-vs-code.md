---
title: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio Code
description: Tworzenie prostej funkcji wyzwalanej przez protokół HTTP i publikowanie jej przy użyciu rozszerzenia usługi Azure Functions w programie Visual Studio Code.
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo, devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 4e50f6b2770f9f787c92e7a9c66f72cdbb252a94
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519694"
---
# <a name="quickstart-create-a-function-in-azure-using-visual-studio-code"></a>Szybki start: Tworzenie funkcji na platformie Azure przy użyciu programu Visual Studio Code

::: zone pivot="programming-language-csharp"
W tym artykule użyto Visual Studio Code do utworzenia funkcji opartej na bibliotece języka C#, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.
::: zone-end
::: zone pivot="programming-language-javascript"
W tym artykule opisano tworzenie funkcji języka JavaScript reagującej na żądania HTTP przy użyciu Visual Studio Code. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.
::: zone-end
::: zone pivot="programming-language-typescript"
W tym artykule opisano tworzenie funkcji języka TypeScript reagującej na żądania HTTP przy użyciu Visual Studio Code. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.
::: zone-end 
::: zone pivot="programming-language-powershell"
W tym artykule opisano tworzenie funkcji programu PowerShell reagującej na żądania HTTP przy użyciu Visual Studio Code. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.
::: zone-end
::: zone pivot="programming-language-python"
W tym artykule użyto Visual Studio Code do utworzenia funkcji języka Python, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.
::: zone-end
::: zone pivot="programming-language-java"
W tym artykule opisano tworzenie funkcji języka Java reagującej na żądania HTTP przy użyciu Visual Studio Code. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.
::: zone-end

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
Istnieje również wersja tego artykułu [oparta na interfejsie wiersza polecenia](functions-create-first-azure-function-azure-cli.md) .
::: zone-end
::: zone pivot="programming-language-java"
> [!NOTE]
> Jeśli Visual Studio Code nie jest preferowanym narzędziem programistycznym, zapoznaj się z naszymi samouczkami dotyczącymi deweloperów języka Java przy użyciu pomysłów [Maven](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java), [Gradle](./functions-create-first-java-gradle.md) i [IntelliJ](/azure/developer/java/toolkit-for-intellij/quickstart-functions).
::: zone-end

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"
- [Node.js](https://nodejs.org/), wymagany przez system Windows for npm. Tylko [aktywne wersje LTS LTS i Maintenance](https://nodejs.org/about/releases/). Użyj `node --version` polecenia, aby sprawdzić wersję.
    Nie jest wymagana do lokalnego programowania w systemach macOS i Linux. 
::: zone-end 
::: zone pivot="programming-language-javascript,programming-language-typescript"
- [Node.js](https://nodejs.org/), Active LTS i konserwacja LTS wersje (zalecane 10.14.1). Użyj `node --version` polecenia, aby sprawdzić wersję.
::: zone-end
::: zone pivot="programming-language-python"
- Środowisko [python 3,8](https://www.python.org/downloads/release/python-381/), [Python 3,7](https://www.python.org/downloads/release/python-375/), [python 3,6](https://www.python.org/downloads/release/python-368/) jest obsługiwane przez Azure Functions (x64).
::: zone-end 
::: zone pivot="programming-language-powershell"
- [Program PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

- [.Net core 3,1](https://www.microsoft.com/net/download) i [.NET Core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.2)
::: zone-end
::: zone pivot="programming-language-java"
- [Zestaw Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8 lub 11.

- [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.
::: zone-end
- [Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"
- [Rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) dla Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-python"
- [Rozszerzenie Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) dla Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
- [Rozszerzenie programu PowerShell dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).
::: zone-end
::: zone pivot="programming-language-java"
- [Pakiet rozszerzeń Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
::: zone-end

- [Azure Functions rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Visual Studio Code.

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny

W tej sekcji użyjesz Visual Studio Code, aby utworzyć projekt lokalnego Azure Functions w wybranym języku. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure. 

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz ikonę **Utwórz nowy projekt...** .

    ![Wybierz pozycję Utwórz nowy projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz **pozycję Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje na ekranie:

    ::: zone pivot="programming-language-csharp"
    - **Wybierz język projektu funkcji**: Wybierz `C#` .
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - **Wybierz język projektu funkcji**: Wybierz `JavaScript` .
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - **Wybierz język projektu funkcji**: Wybierz `TypeScript` .
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - **Wybierz język projektu funkcji**: Wybierz `PowerShell` .
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - **Wybierz język projektu funkcji**: Wybierz `Python` .

    - **Wybierz alias języka Python, aby utworzyć środowisko wirtualne**: Wybierz lokalizację interpretera języka Python. Jeśli lokalizacja nie jest wyświetlana, wpisz pełną ścieżkę do pliku binarnego języka Python.
    ::: zone-end

    ::: zone pivot="programming-language-java"
    - **Wybierz język projektu funkcji**: Wybierz `Java` .

    - **Wybierz wersję środowiska Java**: Wybierz `Java 8` lub `Java 11` , na których funkcja działa na platformie Azure. Wybierz wersję języka Java, która została zweryfikowana lokalnie.

    - **Podaj identyfikator grupy**: Wybierz `com.function` .

    - **Podaj identyfikator artefaktu**: Wybierz `myFunction` .

    - **Podaj wersję**: Wybierz `1.0-SNAPSHOT` .

    - **Podaj nazwę pakietu**: wybierz opcję `com.function` .

    - **Podaj nazwę aplikacji**: Wybierz `myFunction-12345` .
    ::: zone-end
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    - **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz `HTTP trigger` .
  
    - **Podaj nazwę funkcji**: type `HttpExample` .
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    - **Podaj przestrzeń nazw**: type `My.Functions` .
    ::: zone-end
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    - **Poziom autoryzacji**: Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji. Aby dowiedzieć się więcej o poziomie autoryzacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).
    ::: zone-end
    - **Wybierz, w jaki sposób chcesz otworzyć projekt**: Wybierz `Add to workspace` .

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z wyzwalaczem HTTP. Pliki projektu lokalnego można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej na temat tworzonych plików, zobacz [pliki wygenerowanego projektu](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, można użyć Visual Studio Code do opublikowania projektu bezpośrednio na platformie Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Uruchamianie funkcji na platformie Azure

1. Wróć do obszaru **Azure: Functions** na pasku bocznym, a następnie rozwiń nową aplikację funkcji w ramach subskrypcji. Rozwiń węzeł **funkcje**, kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) w **HttpExample**, a następnie wybierz polecenie **Kopiuj adres URL funkcji**.

    ![Skopiuj adres URL funkcji dla nowego wyzwalacza HTTP](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Wklej ten adres URL żądania HTTP na pasku adresu przeglądarki, Dodaj `name` ciąg zapytania na `?name=Functions` końcu tego adresu URL, a następnie wykonaj żądanie. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format:

    ```http
    http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions
    ```

    Poniższy przykład przedstawia odpowiedź w przeglądarce do zdalnego żądania GET zwróconego przez funkcję: 

    ![Odpowiedź funkcji wyświetlona w przeglądarce](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po przeniesieniu do następnego kroku [Dodaj powiązanie kolejki usługi Azure Storage do funkcji](functions-add-output-binding-storage-queue-vs-code.md), musisz zachować wszystkie Twoje zasoby, aby skompilować, co już zostało zrobione.

W przeciwnym razie można wykonać poniższe kroki, aby usunąć aplikację funkcji i powiązane z nią zasoby, aby uniknąć ponoszenia dalszych kosztów.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Aby dowiedzieć się więcej o kosztach funkcji, zobacz [szacowanie kosztów planu zużycia](functions-consumption-costs.md).

## <a name="next-steps"></a>Następne kroki

W programie Visual Studio Code utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP. W następnym artykule można rozszerzyć tę funkcję, dodając powiązanie danych wyjściowych. To powiązanie zapisuje ciąg z żądania HTTP do wiadomości w kolejce usługi Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Dodawanie do funkcji powiązania kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
