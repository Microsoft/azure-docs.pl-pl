---
title: Tworzenie funkcji języka C# przy użyciu Visual Studio Code-Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka C#, a następnie opublikować projekt lokalny do hostingu bezserwerowego w Azure Functions przy użyciu rozszerzenia Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 69a057f92009fd712d39666be223f1cfd99d73b5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748563"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Szybki Start: Tworzenie funkcji języka C# na platformie Azure przy użyciu Visual Studio Code

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [Program PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Inne (Go/Rust)](create-first-function-vs-code-other.md)

W tym artykule użyto Visual Studio Code do utworzenia funkcji opartej na bibliotece języka C#, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go na <abbr title="Środowisko obliczeniowe środowiska uruchomieniowego, w którym wszystkie szczegóły serwera są niewidoczne dla deweloperów aplikacji, upraszczając proces wdrażania kodu i zarządzania nim.">praca bezserwerowa</abbr> Środowisko programu <abbr title="Usługa platformy Azure, która zapewnia nieekonomiczne środowisko obliczeniowe dla aplikacji.">Azure Functions</abbr>.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

Istnieje również wersja tego artykułu [oparta na interfejsie wiersza polecenia](create-first-function-cli-csharp.md) .
    
## <a name="1-configure-your-environment"></a>1. Skonfiguruj środowisko

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Platforma Azure <abbr title="Profil, który przechowuje informacje o rozliczeniach dotyczące użycia platformy Azure.">account</abbr> z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzane są zasoby na platformie Azure, zwykle skojarzone z osobą lub działem w organizacji.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) w wersji 3. x.

+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Rozszerzenie obsługujące język C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) dla programu Visual Studio Code.  

+ [Rozszerzenie obsługujące usługę Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code.

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2. Utwórz projekt lokalny

W tej sekcji użyjesz Visual Studio Code do utworzenia lokalnego <abbr title="Kontener logiczny dla jednej lub kilku funkcji, które mogą być wdrażane i zarządzane wspólnie.">Projekt Azure Functions</abbr> w języku C#. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure.

1. Wybierz ikonę platformy Azure w obszarze <abbr title="Pionowa grupa ikon po lewej stronie okna Visual Studio Code.">Pasek działania</abbr>, a następnie w obszarze **Azure: Functions** wybierz ikonę **Utwórz nowy projekt...** .

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `C#`.

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz plik `HTTP trigger`.

    + **Podaj nazwę funkcji**: Wpisz `HttpExample`.

    + **Podaj przestrzeń nazw**: Wpisz `My.Functions`.

    + **Poziom autoryzacji**: Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji. Aby dowiedzieć się więcej na temat poziomów autoryzacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions przy użyciu protokołu HTTP <abbr title="Typ zdarzenia, które wywołuje kod funkcji, taki jak żądanie HTTP, komunikat kolejki lub określony czas.">uruchamiać</abbr>. Pliki projektu lokalnego można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej na temat tworzonych plików, zobacz [pliki wygenerowanego projektu](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, można użyć Visual Studio Code do opublikowania projektu bezpośrednio na platformie Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5. Opublikuj projekt na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w ramach subskrypcji platformy Azure, a następnie wdrożono swój kod. 

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure. 

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](./media/functions-create-first-function-vs-code/function-app-publish-project.png)

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz folder**: Wybierz folder z obszaru roboczego lub przejdź do folderu, który zawiera aplikację funkcji. Nie zobaczysz tego, jeśli masz już otwartą prawidłową aplikację funkcji.

    + **Wybierz subskrypcję**: wybierz subskrypcję do użycia. Nie zobaczysz tego, jeśli masz tylko jedną subskrypcję.

    + **Wybierz aplikację funkcji na platformie Azure**: Wybierz plik `+ Create new Function App`. (Nie należy wybierać `Advanced` opcji, która nie jest objęta tym artykułem).

    + **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest prawidłowa w ścieżce URL. Wpisana nazwa jest sprawdzana w celu upewnienia się, że jest <abbr title="Nazwa musi być unikatowa we wszystkich projektach funkcji używanych przez wszystkich klientów platformy Azure globalnie. Zwykle używasz kombinacji nazwy prywatnej lub firmy, nazwy aplikacji i identyfikatora liczbowego, jak w contoso-bizapp-Func-20">unikatowy w Azure Functions</abbr>. 

    + **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie.

    Rozszerzenie pokazuje stan poszczególnych zasobów w miarę ich tworzenia na platformie Azure w obszarze powiadomień.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Powiadomienie o tworzeniu zasobów platformy Azure":::

1. Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji przy użyciu nazw opartych na nazwie aplikacji funkcji:

    + **Grupa zasobów**, która jest kontenerem logicznym dla powiązanych zasobów.
    + Standardowe **konto usługi Azure Storage**, które zachowuje stan i inne informacje o projektach.
    + **Plan zużycia**, który definiuje podstawowego hosta dla aplikacji funkcji bezserwerowej. 
    + **Aplikacja funkcji**, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów w ramach tego samego planu hostingu.
    + **Wystąpienie Application Insights** połączone z aplikacją funkcji, która śledzi użycie funkcji bezserwerowej.

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. 

    > [!TIP]
    > Domyślnie zasoby platformy Azure wymagane przez aplikację funkcji są tworzone w oparciu o podaną nazwę aplikacji funkcji. Domyślnie są one również tworzone w tej samej nowej grupie zasobów, w której znajduje się aplikacja funkcji. Jeśli chcesz dostosować nazwy tych zasobów lub użyć ponownie istniejących zasobów, musisz zamiast tego [opublikować projekt przy użyciu opcji Zaawansowane tworzenie](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).


1. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure. W przypadku odrzucenia powiadomienia wybierz ikonę dzwonka w prawym dolnym rogu, aby zobaczyć ją ponownie.

    ![Utwórz pełne powiadomienie](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6. Uruchom funkcję na platformie Azure

1. Wróć do obszaru **Azure: Functions** na pasku bocznym, rozwiń swoją subskrypcję, nową aplikację funkcji i **funkcje**. Kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) `HttpExample` funkcję i wybierz pozycję **Wykonaj funkcję teraz...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Wykonaj funkcję teraz na platformie Azure z Visual Studio Code":::

1. W obszarze **wprowadź treść żądania** zobaczysz wartość treść komunikatu żądania `{ "name": "Azure" }` .

    Naciśnij klawisz ENTER, aby wysłać ten komunikat żądania do funkcji.  

1. Gdy funkcja zostanie wykonana na platformie Azure i zwróci odpowiedź, w Visual Studio Code zostanie zgłoszone powiadomienie.

## <a name="5-clean-up-resources"></a>5. Wyczyść zasoby

W przypadku kontynuowania [następnego kroku](#next-steps) i dodania <abbr title="Oznacza to, że funkcja jest skojarzona z kolejką magazynu, dzięki czemu może tworzyć komunikaty w kolejce.">Powiązanie danych wyjściowych kolejki usługi Azure Storage</abbr> do funkcji należy zachować wszystkie Twoje zasoby, aby skompilować już wykonane działania.

W przeciwnym razie można wykonać poniższe kroki, aby usunąć aplikację funkcji i powiązane z nią zasoby, aby uniknąć ponoszenia dalszych kosztów.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Aby dowiedzieć się więcej o kosztach funkcji, zobacz [szacowanie kosztów planu zużycia](functions-consumption-costs.md).

## <a name="next-steps"></a>Następne kroki

W programie Visual Studio Code utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP. W następnym artykule można rozszerzyć tę funkcję, dodając dane wyjściowe <abbr title="Deklaratywne połączenie między funkcją i innymi zasobami. Powiązanie danych wejściowych zawiera dane do funkcji; powiązanie danych wyjściowych udostępnia dane z funkcji do innych zasobów.">powiązanie</abbr>. To powiązanie zapisuje ciąg z żądania HTTP do wiadomości w kolejce usługi Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
