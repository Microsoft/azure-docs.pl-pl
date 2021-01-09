---
title: Tworzenie funkcji języka Java w Azure Functions przy użyciu IntelliJ
description: Dowiedz się, jak za pomocą IntelliJ utworzyć prostą funkcję języka Java wyzwalaną przez protokół HTTP, która następnie zostanie uruchomiona w środowisku bezserwerowym na platformie Azure.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: ed8948ddeddf25272355cd1dc06d4e95c52f7f62
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035261"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>Tworzenie pierwszej funkcji języka Java na platformie Azure przy użyciu IntelliJ

Ten artykuł zawiera następujące informacje:
- Jak utworzyć funkcję języka Java wyzwalaną przez protokół HTTP w projekcie POMYSŁu IntelliJ.
- Procedura testowania i debugowania projektu w zintegrowanym środowisku programistycznym (IDE) na własnym komputerze.
- Instrukcje dotyczące wdrażania projektu funkcji w Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby tworzyć i publikować funkcje języka Java na platformie Azure przy użyciu programu IntelliJ, Zainstaluj następujące oprogramowanie:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Pakiet [Java Development Kit (JDK) dla platformy Azure](/azure/developer/java/fundamentals/java-jdk-long-term-support) dla języka Java 8
+ Zainstalowane środowisko [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) w wersji Ultimate Edition lub Community Edition
+ [Maven 3.5.0 +](https://maven.apache.org/download.cgi)
+ Najnowsze [podstawowe narzędzia funkcji](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Instalacja i logowanie

1. W oknie dialogowym Settings/Preferences (Ustawienia/preferencje) (Ctrl+Alt+S) środowiska IntelliJ IDEA wybierz pozycję **Plugins** (Wtyczki). Następnie znajdź pozycję **Azure Toolkit for IntelliJ** w witrynie **Marketplace** i kliknij pozycję **Install** (Zainstaluj). Po zainstalowaniu kliknij przycisk **Restart** (Uruchom ponownie), aby aktywować wtyczkę. 

    ![Wtyczka Azure Toolkit for IntelliJ w portalu Marketplace][marketplace]

2. Aby zalogować się do konta platformy Azure, otwórz pasek boczny programu **Azure Explorer** i kliknij ikonę **Azure Sign In** (Logowanie do platformy Azure) w górnej części paska (lub z menu IDEA **Tools/Azure/Azure Sign in** [Narzędzia/Azure/Logowanie Azure]).
    ![Polecenie IntelliJ Azure Sign In (Logowanie Azure)][intellij-azure-login]

3. W oknie **Azure Sign In** (Logowanie do platformy Azure) wybierz pozycję **Device Login** (Logowanie do urządzenia), a następnie kliknij pozycję **Sign in** (Zaloguj się) ([inne opcje logowania](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Okno logowania do platformy Azure z wybraną opcją logowania do urządzenia][intellij-azure-popup]

4. W oknie dialogowym **Azure Device Login** (Logowanie Azure do urządzenia) kliknij pozycję **Copy&Open** (Kopiuj i otwórz).

   ![Okno dialogowe logowania do platformy Azure][intellij-azure-copycode]

5. W przeglądarce wklej kod urządzenia (skopiowany po kliknięciu pozycji **Copy&Open** (Kopiuj i otwórz) w ostatnim kroku), a następnie kliknij przycisk **Next** (Dalej).

   ![Przeglądarka z oknem logowania do urządzenia][intellij-azure-link-ms-account]

6. W oknie dialogowym **Select Subscriptions** (Wybieranie subskrypcji) wybierz subskrypcje do użycia, a następnie kliknij przycisk **OK**.

   ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Utwórz projekt lokalny

W tej sekcji użyto Azure Toolkit for IntelliJ do utworzenia projektu Azure Functions lokalnego. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure. 

1. Otwórz okno dialogowe Witamy w IntelliJ, wybierz pozycję *Utwórz nowy projekt* , aby otworzyć Kreatora nowego projektu, wybierz pozycję *Azure Functions*.

    ![Utwórz projekt funkcji](media/functions-create-first-java-intellij/create-functions-project.png)

1. Wybierz *wyzwalacz http*, a następnie kliknij przycisk *dalej* i postępuj zgodnie z instrukcjami kreatora, aby przejść do wszystkich konfiguracji na następujących stronach: Potwierdź lokalizację projektu, a następnie kliknij przycisk *Zakończ*; Intellj pomysł spowoduje otwarcie nowego projektu.

    ![Zakończenie tworzenia projektu funkcji](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>Lokalne uruchamianie projektu

1. Przejdź do `src/main/java/org/example/functions/HttpTriggerFunction.java` , aby wyświetlić wygenerowany kod. W wierszu *17* zobaczysz, że jest zielony przycisk *Uruchom* , kliknij go i wybierz polecenie *Uruchom "Azure-Function-egzamin..."* zobaczysz, że aplikacja funkcji działa lokalnie z kilkoma dziennikami.

    ![Projekt lokalnego uruchomienia](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Dane wyjściowe lokalnego uruchomienia projektu](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Funkcję można wypróbować, uzyskując dostęp do wydrukowanego punktu końcowego z przeglądarki, na przykład `http://localhost:7071/api/HttpTrigger-Java?name=Azure` .

    ![Wynik testu funkcji uruchamiania lokalnego](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Dziennik jest również drukowany na Twoim POMYSŁie, a teraz Zatrzymaj aplikację funkcji, klikając przycisk *Zatrzymaj* .

    ![Dziennik testu funkcji uruchamiania lokalnego](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>Debugowanie projektu lokalnie

1. Aby debugować kod funkcji w projekcie lokalnie, wybierz przycisk *Debuguj* na pasku narzędzi. Jeśli nie widzisz paska narzędzi, włącz go, wybierając **Widok**  >    >  **pasek narzędzi** wyglądu.

    ![Przycisk aplikacji funkcji debugowania lokalnego](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Kliknij wiersz *20* pliku `src/main/java/org/example/functions/HttpTriggerFunction.java` , aby dodać punkt przerwania, ponownie uzyskaj dostęp do punktu końcowego `http://localhost:7071/api/HttpTrigger-Java?name=Azure` , aby znaleźć punkt przerwania, możesz wypróbować więcej funkcji debugowania, takich jak *krok*, *czujka*, *Ocena*. Zatrzymaj sesję debugowania, klikając przycisk Zatrzymaj.

    ![Podział aplikacji funkcji debugowania lokalnego](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>Wdrażanie projektu na platformie Azure

1. Kliknij prawym przyciskiem myszy projekt w IntelliJ Project Explorer, wybierz pozycję *Azure-> Wdróż do Azure Functions*

    ![Wdrażanie projektu na platformie Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Jeśli nie masz jeszcze żadnych aplikacja funkcji, kliknij przycisk *nie dostępna funkcja, a następnie kliknij, aby utworzyć nowy*.

    ![Tworzenie aplikacji funkcji na platformie Azure](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Wpisz nazwę aplikacji funkcji i wybierz odpowiednią subskrypcję/platformę/grupę zasobów/App Service plan, a następnie w tym miejscu możesz utworzyć plan grupy zasobów/App Service. Następnie pozostaw ustawienia aplikacji bez zmian, kliknij przycisk *OK* i poczekaj kilka minut na utworzenie nowej aplikacji funkcji. Po *utworzeniu nowego aplikacja funkcji...* pasek postępu znika.

    ![Wdróż aplikację funkcji w Kreatorze tworzenia aplikacji platformy Azure](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Wybierz aplikację funkcji, w której chcesz wdrożyć program (nowo utworzona aplikacja funkcji zostanie automatycznie zaznaczona). Kliknij przycisk *Uruchom* , aby wdrożyć swoje funkcje.

    ![Zrzut ekranu przedstawia okno dialogowe wdrażanie Azure Functions.](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Wdróż aplikację funkcji w usłudze Azure log](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>Zarządzaj aplikacjami funkcji z POMYSŁu

1. Możesz zarządzać aplikacjami funkcji za pomocą *Eksploratora platformy Azure* w Twoim pomysłie, klikając pozycję *aplikacja funkcji*, zobaczysz w tym miejscu wszystkie aplikacje funkcji.

    ![Wyświetlanie aplikacji funkcji w Eksploratorze](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Kliknij, aby wybrać jedną z aplikacji funkcji, a następnie kliknij prawym przyciskiem myszy pozycję *Pokaż właściwości* , aby otworzyć stronę szczegółów. 

    ![Pokaż właściwości aplikacji funkcji](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Kliknij prawym przyciskiem myszy aplikację funkcji *HttpTrigger-Java* , a następnie wybierz pozycję *wyzwalacz funkcja*, zobaczysz, że przeglądarka zostanie otwarta z adresem URL wyzwalacza.

    ![Zrzut ekranu przedstawia przeglądarkę z p R L.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Dodaj więcej funkcji do projektu

1. Kliknij prawym przyciskiem myszy pakiet Package *. example. Functions* i wybierz polecenie *New-> klasy funkcji platformy Azure*. 

    ![Dodawanie funkcji do wpisu projektu](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Wypełnij pola Nazwa klasy *HttpTest* i wybierz pozycję *HttpTrigger* w Kreatorze tworzenia klasy funkcji, a następnie kliknij przycisk *OK* , aby utworzyć, w ten sposób można utworzyć nowe funkcje zgodnie z potrzebami.

    ![Zrzut ekranu przedstawia okno dialogowe Tworzenie klasy funkcji.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Dodawanie funkcji do danych wyjściowych projektu](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Czyszczenie funkcji

1. Usuwanie funkcji w Eksploratorze Azure
      
      ![Zrzut ekranu przedstawia polecenie Usuń wybrane z menu kontekstowego.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Następne kroki

Utworzono projekt Java z funkcją wyzwalaną przez protokół HTTP, można go uruchomić na komputerze lokalnym i wdrożyć na platformie Azure. Teraz możesz rozłożyć funkcję przez...

> [!div class="nextstepaction"]
> [Dodawanie powiązania danych wyjściowych kolejki usługi Azure Storage](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
