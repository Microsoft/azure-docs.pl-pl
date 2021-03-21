---
title: Tworzenie funkcji języka C# przy użyciu Visual Studio Code-Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka C#, a następnie opublikować projekt lokalny do hostingu bezserwerowego w Azure Functions przy użyciu rozszerzenia Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-csharp-ieux
ms.openlocfilehash: b0c659c1b3bf373b7754dd9b058c74da35cc2e0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704790"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Szybki Start: Tworzenie funkcji języka C# na platformie Azure przy użyciu Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

W tym artykule użyto Visual Studio Code do utworzenia funkcji opartej na bibliotece języka C#, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

Istnieje również wersja tego artykułu [oparta na interfejsie wiersza polecenia](create-first-function-cli-csharp.md) .

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) w wersji 3. x.

+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Rozszerzenie obsługujące język C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) dla programu Visual Studio Code.  

+ [Rozszerzenie obsługujące usługę Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code.

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny

W tej sekcji użyjesz Visual Studio Code, aby utworzyć projekt Azure Functions lokalnych w języku C#. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure.

1. Wybierz ikonę platformy Azure na pasku działań, a następnie w obszarze **Azure: Funkcje** wybierz ikonę **Utwórz nowy projekt...**.

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `C#`.

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz plik `HTTP trigger`.

    + **Podaj nazwę funkcji**: Wpisz `HttpExample`.

    + **Podaj przestrzeń nazw**: Wpisz `My.Functions`.

    + **Poziom autoryzacji**: Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji. Aby dowiedzieć się więcej o poziomie autoryzacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z wyzwalaczem HTTP. Pliki projektu lokalnego można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej na temat tworzonych plików, zobacz [pliki wygenerowanego projektu](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, można użyć Visual Studio Code do opublikowania projektu bezpośrednio na platformie Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Następne kroki

Użyto [Visual Studio Code](functions-develop-vs-code.md?tabs=csharp) , aby utworzyć aplikację funkcji z prostą funkcją wyzwalaną przez protokół http. W następnym artykule można rozszerzyć tę funkcję, łącząc się z usługą Azure Storage. Aby dowiedzieć się więcej na temat nawiązywania połączenia z innymi usługami platformy Azure, zobacz [Dodawanie powiązań do istniejącej funkcji w Azure Functions](add-bindings-existing-function.md?tabs=csharp). 

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
