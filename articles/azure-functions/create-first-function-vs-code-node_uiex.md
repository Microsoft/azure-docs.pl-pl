---
title: Tworzenie funkcji języka JavaScript przy użyciu Visual Studio Code-Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka JavaScript, a następnie opublikować lokalny projekt Node.js do hostingu bezserwerowego w Azure Functions przy użyciu rozszerzenia Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom:
- devx-track-js
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cf47d04da51db898e667ef8b31d42d79c9f354e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748593"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>Szybki Start: Tworzenie funkcji języka JavaScript na platformie Azure przy użyciu Visual Studio Code

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [JavaScript](create-first-function-vs-code-node.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [Program PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Inne (Go/Rust)](create-first-function-vs-code-other.md)

Użyj Visual Studio Code, aby utworzyć funkcję języka JavaScript, która reaguje na żądania HTTP. Przetestuj kod lokalnie, a następnie wdróż go w środowisku bezserwerowym Azure Functions.

Po ukończeniu tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej <abbr title="Konto platformy Azure jest globalną unikatową jednostką, która umożliwia dostęp do usług platformy Azure i subskrypcji platformy Azure.">Konto platformy Azure</abbr>.

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Konto platformy Azure z <abbr title="Subskrypcja platformy Azure to kontener logiczny, który służy do aprowizowania zasobów na platformie Azure. Są w niej przechowywane szczegółowe informacje o wszystkich posiadanych zasobach, takich jak maszyny wirtualne, bazy danych i inne.">aktywna subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js 10.14.1 +](https://nodejs.org/)

+ [Visual Studio Code](https://code.visualstudio.com/)

+ [Azure Functions rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Visual Studio Code.

+ [Narzędzia Azure Functions Core](functions-run-local.md?tabs=linux%2Ccsharp%2Cbash#install-the-azure-functions-core-tools)

<hr/>
<br/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> Utwórz projekt funkcji lokalnych

1. Wybierz ikonę platformy Azure w obszarze <abbr title="">Pasek działania</abbr>, a następnie w obszarze **Azure: Functions** wybierz ikonę **Utwórz nowy projekt...** .

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Wybierz lokalizację katalogu** dla obszaru roboczego projektu, a następnie wybierz **pozycję Wybierz**. 

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `JavaScript`.

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz plik `HTTP trigger`.

    + **Podaj nazwę funkcji**: Wpisz `HttpExample`.

    + **Poziom autoryzacji**: Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji.

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.




<br/>
<details>
<summary><strong>Nie można utworzyć projektu funkcji?</strong></summary>

Najczęstsze problemy, które należy rozwiązać podczas tworzenia projektu funkcji lokalnych:
* Nie zainstalowano rozszerzenia Azure Functions. 
</details>

<hr/>
<br/>

## <a name="3-run-the-function-locally"></a>3. Uruchom funkcję lokalnie


1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić projekt aplikacji funkcji. 

1. W **terminalu** zapoznaj się z punktem końcowym adresu URL funkcji uruchomionej lokalnie.

    ![Funkcja lokalna VS Code danych wyjściowych](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Skopiuj następujący adres URL i wklej go w przeglądarce internetowej, a następnie naciśnij klawisz ENTER.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Wyświetl zwracaną odpowiedź.


    ![Przeglądarka — przykładowe dane wyjściowe hosta lokalnego](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Wyświetl informacje w panelu **terminalu** dotyczące żądania.

    ![Rozpoczęcie VS Code hosta zadań — dane wyjściowe terminalu](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Naciśnij <kbd>klawisze CTRL + C</kbd> , aby zatrzymać podstawowe narzędzia i rozłączyć debuger.

<br/>
<details>
<summary><strong>Nie można uruchomić funkcji lokalnie?</strong></summary>

Najczęstsze problemy, które należy rozwiązać podczas uruchamiania projektu funkcji lokalnych, to:
* Nie masz zainstalowanych podstawowych narzędzi. 
*  Jeśli masz problemy z uruchamianiem w systemie Windows, upewnij się, że domyślna powłoka terminalu dla Visual Studio Code nie jest ustawiona na WSL bash. 
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

    + **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest unikatowa w obrębie platformy Azure w ścieżce URL. Wpisana nazwa jest weryfikowana, aby zapewnić globalną unikatowość.

    + **Wybierz środowisko uruchomieniowe**: wybierz wersję Node.js, która była uruchomiona lokalnie. Możesz użyć polecenia, `node --version` Aby sprawdzić wersję.

    + **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie. 

1. Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz pozycję **Wyświetl dane wyjściowe** , aby zobaczyć wyniki tworzenia i wdrażania. 
    
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
1. Na pasku bocznym **Azure: Functions** rozwiń nową aplikację funkcji. 
1. Rozwiń węzeł **funkcje**, a następnie kliknij prawym przyciskiem myszy pozycję **HttpExample**, a następnie wybierz pozycję **Wykonaj funkcję teraz...**.

    ![Skopiuj adres URL funkcji dla nowego wyzwalacza HTTP](../../includes/media/functions-vs-code-run-remote/execute-function-now.png)

1. **Naciśnij klawisz ENTER** , aby wysłać do funkcji domyślny komunikat żądania. 

1. Powiadomienie jest zgłaszane w Visual Studio Code po zakończeniu wykonywania funkcji.

<br/>
<details>
<summary><strong>Nie można uruchomić aplikacji funkcji opartej na chmurze?</strong></summary>

* Czy pamiętasz, że chcesz dodać ciąg QueryString na końcu adresu URL?

</details>

<hr/>
<br/>

## <a name="7-clean-up-resources"></a>7. Wyczyść zasoby

Usuń aplikację funkcji i jej zasoby, aby uniknąć ponoszenia dalszych kosztów.

1. W Visual Studio Code wybierz ikonę platformy Azure na pasku działania, a następnie wybierz obszar funkcje na pasku bocznym. 
1. Wybierz aplikację funkcji, a następnie kliknij prawym przyciskiem myszy i wybierz pozycję **Usuń aplikację funkcji..**..

<hr/>
<br/>

## <a name="next-steps"></a>Następne kroki

Rozwiń funkcję, dodając <abbr title="Powiązanie z funkcją jest sposobem deklaratywnego łączenia innego zasobu z funkcją.">powiązanie danych wyjściowych</abbr>. To powiązanie zapisuje ciąg z żądania HTTP do wiadomości w kolejce usługi Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
