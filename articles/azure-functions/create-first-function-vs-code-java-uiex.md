---
title: Tworzenie funkcji języka Java przy użyciu Visual Studio Code-Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Java, a następnie opublikować projekt lokalny do hostingu bezserwerowego w Azure Functions przy użyciu rozszerzenia Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c84bf4ebc73857fa0280ffbcbf46b68c2da630f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031722"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Szybki Start: Tworzenie funkcji języka Java na platformie Azure przy użyciu Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Użyj Visual Studio Code, aby utworzyć funkcję języka Java, która reaguje na żądania HTTP. Przetestuj kod lokalnie, a następnie wdróż go w środowisku bezserwerowym Azure Functions.

Po ukończeniu tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej <abbr title="Profil, który przechowuje informacje o rozliczeniach dotyczące użycia platformy Azure.">Konto platformy Azure</abbr>.

Jeśli Visual Studio Code nie jest preferowanym narzędziem programistycznym, zapoznaj się z naszymi samouczkami dotyczącymi deweloperów języka Java przy użyciu pomysłów [Maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) i [IntelliJ](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Konto platformy Azure z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzane są zasoby na platformie Azure, zwykle skojarzone z osobą lub działem w organizacji.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Zestaw Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8 lub 11.

+ [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.

+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Pakiet rozszerzeń Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ [Rozszerzenie obsługujące usługę Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code.

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> Utwórz projekt funkcji lokalnych

1. Wybierz ikonę platformy Azure na **pasku działania**, a następnie w obszarze **Azure: Functions** wybierz ikonę **Utwórz nowy projekt...** .

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Wybierz lokalizację katalogu** dla obszaru roboczego projektu, a następnie wybierz **pozycję Wybierz**.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `Java`.

    + **Wybierz wersję środowiska Java**: Wybierz `Java 8` lub `Java 11` , wersję języka Java, na której funkcje są uruchamiane na platformie Azure. Wybierz wersję języka Java, która została zweryfikowana lokalnie.

    + **Podaj identyfikator grupy**: Wybierz `com.function` .

    + **Podaj identyfikator artefaktu**: Wybierz `myFunction` .

    + **Podaj wersję**: Wybierz `1.0-SNAPSHOT` .

    + **Podaj nazwę pakietu**: wybierz opcję `com.function` .

    + **Podaj nazwę aplikacji**: Wybierz `myFunction-12345` .

    + **Poziom autoryzacji**: Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji.

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.

<br/>

<details>
<summary><strong>Nie można utworzyć projektu funkcji?</strong></summary>

Najczęstsze problemy, które należy rozwiązać podczas tworzenia projektu funkcji lokalnych:
* Nie zainstalowano rozszerzenia Azure Functions. 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Uruchom funkcję lokalnie

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić projekt aplikacji funkcji.

1. W **terminalu** zapoznaj się z punktem końcowym adresu URL funkcji uruchomionej lokalnie.

    ![Funkcja lokalna VS Code danych wyjściowych](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Po uruchomieniu podstawowych narzędzi przejdź do obszaru **Azure: Functions** . W obszarze **funkcje** rozwiń pozycję funkcje **projektu lokalnego**  >  . Kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) `HttpExample` funkcję i wybierz pozycję **Wykonaj funkcję teraz...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Wykonaj funkcję teraz z Visual Studio Code":::

1. W obszarze **wprowadź treść żądania** zobaczysz wartość treść komunikatu żądania `{ "name": "Azure" }` . Naciśnij klawisz <kbd>Enter</kbd> , aby wysłać ten komunikat żądania do funkcji.  

1. Gdy funkcja jest wykonywana lokalnie i zwraca odpowiedź, zostanie zgłoszone powiadomienie w Visual Studio Code. Informacje o wykonywaniu funkcji są wyświetlane w panelu **terminalu** .

1. Naciśnij <kbd>klawisze CTRL + C</kbd> , aby zatrzymać podstawowe narzędzia i rozłączyć debuger.

<br/>

<details>
<summary><strong>Nie można uruchomić funkcji lokalnie?</strong></summary>

Najczęstsze problemy, które należy rozwiązać podczas uruchamiania projektu funkcji lokalnych, to:
* Nie masz zainstalowanych podstawowych narzędzi. 
*  Jeśli masz problemy z uruchamianiem w systemie Windows, upewnij się, że domyślna powłoka terminalu dla Visual Studio Code nie jest ustawiona na WSL bash. 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4. Zaloguj się do platformy Azure

Aby opublikować aplikację, zaloguj się do platformy Azure. Jeśli użytkownik jest już zalogowany, przejdź do następnej sekcji.

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz pozycję **Zaloguj się do platformy Azure...**.

    ![Zaloguj się do platformy Azure w ramach VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Po wyświetleniu monitu w przeglądarce **Wybierz swoje konto platformy Azure** i **Zaloguj się** przy użyciu poświadczeń konta platformy Azure.

1. Po pomyślnym zalogowaniu Zamknij nowe okno przeglądarki i wróć do Visual Studio Code.

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5. Opublikuj projekt na platformie Azure

Twoje pierwsze wdrożenie kodu obejmuje utworzenie zasobu funkcji w ramach subskrypcji platformy Azure.

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz folder**: Wybierz folder, który zawiera aplikację funkcji. 

    + **Wybierz subskrypcję**: wybierz subskrypcję do użycia. Nie zobaczysz tego, jeśli masz tylko jedną subskrypcję.

    + **Wybierz aplikację funkcji na platformie Azure**: Wybierz plik `Create new Function App`.

    + **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest unikatowa w obrębie platformy Azure w ścieżce URL. Wpisana nazwa jest weryfikowana, aby zapewnić globalną unikatowość.

    - **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie.

1. Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz pozycję **Wyświetl dane wyjściowe** , aby zobaczyć wyniki tworzenia i wdrażania.

    ![Utwórz pełne powiadomienie](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

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

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6. Uruchom funkcję na platformie Azure

1. Wróć do obszaru **Azure: Functions** na pasku bocznym, rozwiń swoją subskrypcję, nową aplikację funkcji i **funkcje**. Kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) `HttpExample` funkcję i wybierz pozycję **Wykonaj funkcję teraz...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Wykonaj funkcję teraz na platformie Azure z Visual Studio Code":::

1. W obszarze **wprowadź treść żądania** zobaczysz wartość treść komunikatu żądania `{ "name": "Azure" }` . Naciśnij klawisz ENTER, aby wysłać ten komunikat żądania do funkcji.  

1. Gdy funkcja zostanie wykonana na platformie Azure i zwróci odpowiedź, w Visual Studio Code zostanie zgłoszone powiadomienie.

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7. Wyczyść zasoby

Jeśli nie planujesz przejść do [następnego kroku](#next-steps), Usuń aplikację funkcji i jej zasoby, aby uniknąć ponoszenia dalszych kosztów.

1. W Visual Studio Code wybierz ikonę platformy Azure na pasku działania, a następnie wybierz obszar funkcje na pasku bocznym.
1. Wybierz aplikację funkcji, a następnie kliknij prawym przyciskiem myszy i wybierz pozycję **Usuń aplikację funkcji..**..

<br/>
<hr/>

## <a name="next-steps"></a>Następne kroki

Rozwiń funkcję, dodając <abbr title="W usłudze Azure Storage istnieje możliwość skojarzenia funkcji z kolejką magazynu, dzięki czemu można tworzyć komunikaty w kolejce.">powiązanie danych wyjściowych</abbr>. To powiązanie zapisuje ciąg z żądania HTTP do wiadomości w kolejce usługi Azure Queue Storage.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
