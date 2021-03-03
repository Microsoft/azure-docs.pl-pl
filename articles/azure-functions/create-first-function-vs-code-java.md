---
title: Tworzenie funkcji języka Java przy użyciu Visual Studio Code-Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Java, a następnie opublikować projekt lokalny do hostingu bezserwerowego w Azure Functions przy użyciu rozszerzenia Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-java-uiex
ms.openlocfilehash: 10ac677fb65428e8b8855c43b319bbebe7bbbb71
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701436"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Szybki Start: Tworzenie funkcji języka Java na platformie Azure przy użyciu Visual Studio Code

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [Java](create-first-function-vs-code-java.md)
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [Program PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Inne (Go/Rust)](create-first-function-vs-code-other.md)

W tym artykule opisano tworzenie funkcji języka Java reagującej na żądania HTTP przy użyciu Visual Studio Code. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

> [!NOTE]
> Jeśli Visual Studio Code nie jest preferowanym narzędziem programistycznym, zapoznaj się z naszymi samouczkami dotyczącymi deweloperów języka Java przy użyciu pomysłów [Maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) i [IntelliJ](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Zestaw Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8 lub 11.

+ [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.

+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Pakiet rozszerzeń Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ [Rozszerzenie obsługujące usługę Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny

W tej sekcji użyjesz Visual Studio Code, aby utworzyć projekt lokalnego Azure Functions w języku Java. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure. 

1. Wybierz ikonę platformy Azure na pasku działań, a następnie w obszarze **Azure: Funkcje** wybierz ikonę **Utwórz nowy projekt...**.

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `Java`.

    + **Wybierz wersję środowiska Java**: Wybierz `Java 8` lub `Java 11` , wersję języka Java, na której funkcje są uruchamiane na platformie Azure. Wybierz wersję języka Java, która została zweryfikowana lokalnie.

    + **Podaj identyfikator grupy**: Wybierz `com.function` .

    + **Podaj identyfikator artefaktu**: Wybierz `myFunction` .

    + **Podaj wersję**: Wybierz `1.0-SNAPSHOT` .

    + **Podaj nazwę pakietu**: wybierz opcję `com.function` .

    + **Podaj nazwę aplikacji**: Wybierz `myFunction-12345` .

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

Użyto [Visual Studio Code](functions-develop-vs-code.md?tabs=java) , aby utworzyć aplikację funkcji z prostą funkcją wyzwalaną przez protokół http. W następnym artykule można rozszerzyć tę funkcję, łącząc się z usługą Azure Storage. Aby dowiedzieć się więcej na temat nawiązywania połączenia z innymi usługami platformy Azure, zobacz [Dodawanie powiązań do istniejącej funkcji w Azure Functions](add-bindings-existing-function.md?tabs=java). 

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
