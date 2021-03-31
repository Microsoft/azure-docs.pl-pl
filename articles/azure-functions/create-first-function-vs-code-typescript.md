---
title: Tworzenie funkcji języka TypeScript przy użyciu Visual Studio Code-Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka TypeScript, a następnie opublikować lokalny projekt Node.js do hostingu bezserwerowego w Azure Functions przy użyciu rozszerzenia Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 4470741c8c7f2f6c9832ea73aa9c6207764bc903
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493542"
---
# <a name="quickstart-create-a-function-in-azure-with-typescript-using-visual-studio-code"></a>Szybki Start: Tworzenie funkcji na platformie Azure za pomocą języka TypeScript przy użyciu Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

W tym artykule opisano tworzenie funkcji języka TypeScript reagującej na żądania HTTP przy użyciu Visual Studio Code. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure. 

Istnieje również wersja tego artykułu [oparta na interfejsie wiersza polecenia](create-first-function-cli-typescript.md) .

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js](https://nodejs.org/), aktywne wersje LTS LTS i Maintenance (zalecane 10.14.1). Użyj `node --version` polecenia, aby sprawdzić wersję.  

+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Rozszerzenie obsługujące usługę Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code.

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny

W tej sekcji użyjesz Visual Studio Code, aby utworzyć projekt lokalnego Azure Functions w języku TypeScript. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure. 

1. Wybierz ikonę platformy Azure na pasku działań, a następnie w obszarze **Azure: Funkcje** wybierz ikonę **Utwórz nowy projekt...**.

    ![Wybieranie pozycji Utwórz nowy projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `TypeScript`.

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz plik `HTTP trigger`.

    + **Podaj nazwę funkcji**: Wpisz `HttpExample`.

    + **Poziom autoryzacji**: Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji. Aby dowiedzieć się więcej o poziomie autoryzacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z wyzwalaczem HTTP. Pliki projektu lokalnego można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej na temat tworzonych plików, zobacz [pliki wygenerowanego projektu](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, można użyć Visual Studio Code do opublikowania projektu bezpośrednio na platformie Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w ramach subskrypcji platformy Azure, a następnie wdrożono swój kod.

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure. 

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz folder**: Wybierz folder z obszaru roboczego lub przejdź do folderu, który zawiera aplikację funkcji. Nie zobaczysz tego, jeśli masz już otwartą prawidłową aplikację funkcji.

    + **Wybierz subskrypcję**: wybierz subskrypcję do użycia. Nie zobaczysz tego, jeśli masz tylko jedną subskrypcję.

    + **Wybierz aplikację funkcji na platformie Azure**: Wybierz plik `+ Create new Function App`. (Nie należy wybierać `Advanced` opcji, która nie jest objęta tym artykułem).

    + **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest prawidłowa w ścieżce URL. Wpisana nazwa zostanie sprawdzona, aby upewnić się, że jest ona unikatowa w Azure Functions. 

    + **Wybierz środowisko uruchomieniowe**: wybierz wersję Node.js, która była uruchomiona lokalnie. Możesz użyć polecenia, `node --version` Aby sprawdzić wersję.

    + **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie.

    Rozszerzenie pokazuje stan poszczególnych zasobów w miarę ich tworzenia na platformie Azure w obszarze powiadomień.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Powiadomienie o tworzeniu zasobów platformy Azure":::

1. Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji przy użyciu nazw opartych na nazwie aplikacji funkcji:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure. W przypadku odrzucenia powiadomienia wybierz ikonę dzwonka w prawym dolnym rogu, aby zobaczyć ją ponownie.

    ![Utwórz pełne powiadomienie](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Następne kroki

Użyto [Visual Studio Code](functions-develop-vs-code.md?tabs=javascript) , aby utworzyć aplikację funkcji z prostą funkcją wyzwalaną przez protokół http. W następnym artykule można rozszerzyć tę funkcję, łącząc się z usługą Azure Storage. Aby dowiedzieć się więcej na temat nawiązywania połączenia z innymi usługami platformy Azure, zobacz [Dodawanie powiązań do istniejącej funkcji w Azure Functions](add-bindings-existing-function.md?tabs=typescript).   

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-typescript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
