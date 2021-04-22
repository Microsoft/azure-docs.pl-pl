---
title: Tworzenie funkcji programu PowerShell przy użyciu Visual Studio Code — Azure Functions
description: Dowiedz się, jak utworzyć funkcję programu PowerShell, a następnie opublikować projekt lokalny w hostingu bez serwera w programie Azure Functions przy użyciu Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 58a2b8a5fb386603361afa9fc26b4d323c487691
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862547"
---
# <a name="quickstart-create-a-powershell-function-in-azure-using-visual-studio-code"></a>Szybki start: tworzenie funkcji programu PowerShell na platformie Azure przy użyciu Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

W tym artykule użyjemy Visual Studio Code do utworzenia funkcji programu PowerShell, która odpowiada na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bez serwera Azure Functions.

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem konta platformy Azure w wysokości nie mniejszej niż kilka centów USD.

Istnieje również wersja tego artykułu [oparta na interfejsie](create-first-function-cli-powershell.md) wiersza polecenia.

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 3.x.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Zarówno [środowisko uruchomieniowe .NET Core 3.1,](https://dotnet.microsoft.com/download) [jak i środowisko uruchomieniowe .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).    

+ Rozszerzenie [programu PowerShell dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

+ [Rozszerzenie obsługujące usługę Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Tworzenie projektu lokalnego

W tej sekcji użyjemy Visual Studio Code, aby utworzyć lokalny projekt Azure Functions w programie PowerShell. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure.

1. Wybierz ikonę platformy Azure na pasku działań, a następnie w obszarze **Azure: Funkcje** wybierz ikonę **Utwórz nowy projekt...**.

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `PowerShell`.

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz plik `HTTP trigger`.

    + **Podaj nazwę funkcji**: Wpisz `HttpExample`.

    + **Poziom autoryzacji:** wybierz `Anonymous` pozycję , która umożliwia każdemu wywołanie punktu końcowego funkcji. Aby dowiedzieć się więcej na temat poziomu autoryzacji, zobacz [Klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z wyzwalaczem HTTP. Lokalne pliki projektu można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej o utworzonych plikach, zobacz [Generated project files (Wygenerowane pliki projektu).](functions-develop-vs-code.md#generated-project-files) 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po sprawdzeniu, czy funkcja działa poprawnie na komputerze lokalnym, nas czas na użycie narzędzia Visual Studio Code publikowania projektu bezpośrednio na platformie Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Następne kroki

Za pomocą [Visual Studio Code](functions-develop-vs-code.md?tabs=powershell) aplikacji funkcji z prostą funkcją wyzwalaną przez protokół HTTP. W następnym artykule rozszerzysz tę funkcję, łącząc się z usługą Azure Storage. Aby dowiedzieć się więcej na temat nawiązywania połączenia z innymi usługami platformy Azure, zobacz Dodawanie powiązań [do istniejącej funkcji](add-bindings-existing-function.md?tabs=powershell)w Azure Functions .  

> [!div class="nextstepaction"]
> [Łączenie z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-powershell)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
