---
title: Tworzenie funkcji języka Python przy użyciu Visual Studio Code-Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Python, a następnie opublikować projekt lokalny do hostingu bezserwerowego w Azure Functions przy użyciu rozszerzenia Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 42f07b76cefed38aad53caba9ba35c74238540fe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031739"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Szybki Start: Tworzenie funkcji na platformie Azure przy użyciu języka Python za pomocą Visual Studio Code

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [Program PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Inne (Go/Rust)](create-first-function-vs-code-other.md)

W tym artykule użyto Visual Studio Code do utworzenia funkcji języka Python, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go na <abbr title="Środowisko obliczeniowe środowiska uruchomieniowego, w którym wszystkie szczegóły serwera są niewidoczne dla deweloperów aplikacji, co upraszcza proces wdrażania kodu i zarządzania nim.">praca bezserwerowa</abbr> Środowisko programu <abbr title="Usługa platformy Azure, która zapewnia nieekonomiczne środowisko obliczeniowe dla aplikacji.">Azure Functions</abbr>.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

Istnieje również wersja tego artykułu [oparta na interfejsie wiersza polecenia](create-first-function-cli-python.md) .

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Platforma Azure <abbr title="Profil, który przechowuje informacje o rozliczeniach dotyczące użycia platformy Azure.">account</abbr> z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzane są zasoby na platformie Azure, zwykle skojarzone z osobą lub działem w organizacji.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) w wersji 3. x.

+ Środowisko [python 3,8](https://www.python.org/downloads/release/python-381/), [Python 3,7](https://www.python.org/downloads/release/python-375/), [python 3,6](https://www.python.org/downloads/release/python-368/) jest obsługiwane przez Azure Functions (x64).

+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Rozszerzenie Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) dla Visual Studio Code.  

+ [Rozszerzenie obsługujące usługę Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code.

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a> Utwórz projekt lokalny

1. Wybierz ikonę platformy Azure w obszarze <abbr title="Pionowa grupa ikon po lewej stronie okna Visual Studio Code.">Pasek działania</abbr>, a następnie w obszarze **Azure: Functions** wybierz ikonę **Utwórz nowy projekt...** .

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `Python`.

    + **Wybierz alias języka Python, aby utworzyć środowisko wirtualne**: Wybierz lokalizację interpretera języka Python. Jeśli lokalizacja nie jest wyświetlana, wpisz pełną ścieżkę do pliku binarnego języka Python.  

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz plik `HTTP trigger`.

    + **Podaj nazwę funkcji**: Wpisz `HttpExample`.

    + **Poziom autoryzacji**: Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji. Aby dowiedzieć się więcej na temat poziomów autoryzacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.

<br/>
<details>
<summary><strong>Nie można utworzyć projektu funkcji?</strong></summary>

Najczęstsze problemy, które należy rozwiązać podczas tworzenia projektu funkcji lokalnych:
* Nie zainstalowano rozszerzenia Azure Functions. 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić projekt aplikacji funkcji.

1. Na panelu **terminalu** Sprawdź punkt końcowy adresu URL funkcji uruchomionej lokalnie.

    ![Funkcja lokalna VS Code danych wyjściowych](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Po uruchomieniu podstawowych narzędzi przejdź do obszaru **Azure: Functions** . W obszarze **funkcje** rozwiń pozycję funkcje **projektu lokalnego**  >  . Kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) `HttpExample` funkcję i wybierz pozycję **Wykonaj funkcję teraz...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Wykonaj funkcję teraz z Visual Studio Code":::

1. W obszarze **wprowadź treść żądania** zobaczysz wartość treść komunikatu żądania `{ "name": "Azure" }` . Naciśnij klawisz ENTER, aby wysłać ten komunikat żądania do funkcji.  

1. Gdy funkcja jest wykonywana lokalnie i zwraca odpowiedź, zostanie zgłoszone powiadomienie w Visual Studio Code. Informacje o wykonywaniu funkcji są wyświetlane w panelu **terminalu** .

1. Naciśnij <kbd>klawisze CTRL + C</kbd> , aby zatrzymać podstawowe narzędzia i rozłączyć debuger.

<br/>
<details>
<summary><strong>Nie można uruchomić funkcji lokalnie?</strong></summary>

Najczęstsze problemy, które należy rozwiązać podczas uruchamiania projektu funkcji lokalnych, to:
* Nie masz zainstalowanych podstawowych narzędzi. 
*  Jeśli masz problemy z uruchamianiem w systemie Windows, upewnij się, że domyślna powłoka terminalu dla Visual Studio Code nie jest ustawiona na **WSL bash**. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. Zaloguj się do platformy Azure

Aby opublikować aplikację, zaloguj się do platformy Azure. Jeśli użytkownik jest już zalogowany, przejdź do następnej sekcji.

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz pozycję **Zaloguj się do platformy Azure...**.

    ![Zaloguj się do platformy Azure w ramach VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Po wyświetleniu monitu w przeglądarce **Wybierz swoje konto platformy Azure** i **Zaloguj się** przy użyciu poświadczeń konta platformy Azure.

1. Po pomyślnym zalogowaniu Zamknij nowe okno przeglądarki i wróć do Visual Studio Code. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. Opublikuj projekt na platformie Azure

Twoje pierwsze wdrożenie kodu obejmuje utworzenie zasobu funkcji w ramach subskrypcji platformy Azure.

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz folder**: Wybierz folder, który zawiera aplikację funkcji.

    + **Wybierz subskrypcję**: wybierz subskrypcję do użycia. Nie zobaczysz tego, jeśli masz tylko jedną subskrypcję.

    + **Wybierz aplikację funkcji na platformie Azure**: Wybierz plik `+ Create new Function App`.

    + **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest prawidłowa w ścieżce URL. Wpisana nazwa jest sprawdzana w celu upewnienia się, że jest <abbr title="Nazwa musi być unikatowa dla wszystkich klientów platformy Azure globalnie. Można na przykład użyć kombinacji nazwy prywatnej lub organizacji, nazwy aplikacji i identyfikatora liczbowego, tak jak w contoso-bizapp-Func-20.">unikatowy na platformie Azure</abbr>. 

    + **Wybierz środowisko uruchomieniowe**: Wybierz wersję języka Python, która była uruchomiona lokalnie. Możesz użyć polecenia, `python --version` Aby sprawdzić wersję.

    + **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie.

    Rozszerzenie pokazuje stan poszczególnych zasobów w miarę ich tworzenia na platformie Azure w obszarze powiadomień.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Powiadomienie o tworzeniu zasobów platformy Azure":::

1. Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz pozycję **Wyświetl dane wyjściowe** , aby wyświetlić wyniki tworzenia i wdrażania. 

    ![Utwórz pełne powiadomienie](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>Nie można opublikować funkcji?</strong></summary>

W tej sekcji utworzono zasoby platformy Azure i wdrożono kod lokalny w aplikacji funkcji. Jeśli to nie powiodło się:

* Przejrzyj dane wyjściowe, aby uzyskać informacje o błędzie. Ikona dzwonka w prawym dolnym rogu jest innym sposobem wyświetlania danych wyjściowych. 
* Czy opublikowano w istniejącej aplikacji funkcji? Ta akcja spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.
</details>


<br/>
<details>
<summary><strong>Jakie zasoby zostały utworzone?</strong></summary>

Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji przy użyciu nazw opartych na nazwie aplikacji funkcji: 
* **Grupa zasobów**: Grupa zasobów jest kontenerem logicznym dla powiązanych zasobów w tym samym regionie.
* **Konto usługi Azure Storage**: zasób magazynu zachowuje stan i inne informacje o projekcie.
* **Plan zużycia**: plan zużycia definiuje podstawowego hosta dla aplikacji funkcji bezserwerowej.
* **Aplikacja funkcji**: aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji i grup funkcji jako jednostki logicznej.
* **Application Insights**: Application Insights śledzi użycie funkcji bezserwerowej.

</details>

<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. Uruchom funkcję na platformie Azure

1. Na pasku bocznym **platformy Azure: funkcje** rozwiń nową aplikację funkcji.
1. Rozwiń węzeł **funkcje**, a następnie kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) `HttpExample` funkcję i wybierz pozycję **Wykonaj funkcję teraz...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Wykonaj funkcję teraz na platformie Azure z Visual Studio Code":::

1. W obszarze **wprowadź treść żądania** zobaczysz wartość treść komunikatu żądania `{ "name": "Azure" }` .

    Naciśnij klawisz ENTER, aby wysłać ten komunikat żądania do funkcji.  

1. Gdy funkcja zostanie wykonana na platformie Azure i zwróci odpowiedź, w Visual Studio Code zostanie zgłoszone powiadomienie.

## <a name="7-clean-up-resources"></a>7. Wyczyść zasoby

W przypadku kontynuowania [następnego kroku](#next-steps) i dodania <abbr title="Oznacza to, że funkcja jest skojarzona z kolejką magazynu, dzięki czemu może tworzyć komunikaty w kolejce.">Powiązanie danych wyjściowych kolejki usługi Azure Storage</abbr> do funkcji należy zachować wszystkie Twoje zasoby, aby skompilować już wykonane działania.

W przeciwnym razie można wykonać poniższe kroki, aby usunąć aplikację funkcji i powiązane z nią zasoby, aby uniknąć ponoszenia dalszych kosztów.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Aby dowiedzieć się więcej o kosztach funkcji, zobacz [szacowanie kosztów planu zużycia](functions-consumption-costs.md).

## <a name="next-steps"></a>Następne kroki

Rozwiń tę funkcję, dodając dane wyjściowe <abbr title="Deklaratywne połączenie między funkcją i innymi zasobami. Powiązanie danych wejściowych zawiera dane do funkcji; powiązanie danych wyjściowych udostępnia dane z funkcji do innych zasobów.">powiązanie</abbr>. To powiązanie zapisuje ciąg z żądania HTTP do wiadomości w kolejce usługi Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Masz problemy? Daj nam znać.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
