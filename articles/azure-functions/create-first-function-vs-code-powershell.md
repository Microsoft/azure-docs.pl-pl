---
title: Tworzenie funkcji programu PowerShell przy użyciu Visual Studio Code-Azure Functions
description: Dowiedz się, jak utworzyć funkcję programu PowerShell, a następnie opublikować projekt lokalny do hostingu bezserwerowego w Azure Functions przy użyciu rozszerzenia Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 5d85c0362daa9dc3b42ed0cc1329329ffa598bee
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637051"
---
# <a name="quickstart-create-a-powershell-function-in-azure-using-visual-studio-code"></a>Szybki Start: Tworzenie funkcji programu PowerShell na platformie Azure przy użyciu Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

W tym artykule opisano tworzenie funkcji programu PowerShell reagującej na żądania HTTP przy użyciu Visual Studio Code. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

Istnieje również wersja tego artykułu [oparta na interfejsie wiersza polecenia](create-first-function-cli-powershell.md) .

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js](https://nodejs.org/), wymagany przez system Windows for npm. Tylko [aktywne wersje LTS LTS i Maintenance](https://nodejs.org/about/releases/). Użyj `node --version` polecenia, aby sprawdzić wersję.
    Nie jest wymagana do lokalnego programowania w systemach macOS i Linux.

+ [Program PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Środowisko [uruchomieniowe programu .net core 3,1](https://www.microsoft.com/net/download) i [.NET Core 2,1 Runtime](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ [Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).    

+ [Rozszerzenie programu PowerShell dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

+ [Azure Functions rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny

W tej sekcji użyjesz Visual Studio Code, aby utworzyć projekt lokalnego Azure Functions w programie PowerShell. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure.

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz ikonę **Utwórz nowy projekt...** .

    ![Wybierz pozycję Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz **pozycję Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje na ekranie:

    + **Wybierz język projektu funkcji** : Wybierz `PowerShell` .

    + **Wybierz szablon dla pierwszej funkcji projektu** : Wybierz `HTTP trigger` .

    + **Podaj nazwę funkcji** : type `HttpExample` .

    + **Poziom autoryzacji** : Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji. Aby dowiedzieć się więcej o poziomie autoryzacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Wybierz, w jaki sposób chcesz otworzyć projekt** : Wybierz `Add to workspace` .

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z wyzwalaczem HTTP. Pliki projektu lokalnego można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej na temat tworzonych plików, zobacz [pliki wygenerowanego projektu](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, można użyć Visual Studio Code do opublikowania projektu bezpośrednio na platformie Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Następne kroki

W programie Visual Studio Code utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP. W następnym artykule można rozszerzyć tę funkcję, dodając powiązanie danych wyjściowych. To powiązanie zapisuje ciąg z żądania HTTP do wiadomości w kolejce usługi Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-powershell)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
