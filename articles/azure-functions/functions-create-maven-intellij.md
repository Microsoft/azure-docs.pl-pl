---
title: Tworzenie funkcji platformy Azure przy użyciu języka Java i IntelliJ
description: Dowiedz się, jak utworzyć i opublikować prostą bezserwerową aplikację wywoływaną przez protokół HTTP na platformie Azure przy użyciu języka Java i IntelliJ.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 19deaa7656cc86d534278464dba1041267ef15b0
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105043"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Tworzenie pierwszej funkcji platformy Azure przy użyciu języka Java i IntelliJ

Ten artykuł zawiera następujące informacje:
- Jak utworzyć projekt funkcji bez użycia [serwera](https://azure.microsoft.com/overview/serverless-computing/) z pomysłem IntelliJ
- Kroki testowania i debugowania funkcji w zintegrowanym środowisku programistycznym (IDE) na własnym komputerze
- Instrukcje dotyczące wdrażania projektu funkcji w Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby opracować funkcję przy użyciu języka Java i IntelliJ, Zainstaluj następujące oprogramowanie:

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

## <a name="run-the-function-app-locally"></a>Uruchamianie aplikacja funkcji lokalnie

1. Przejdź do `src/main/java/org/example/functions/HttpTriggerFunction.java` , aby wyświetlić wygenerowany kod. W wierszu *17*zobaczysz, że jest zielony przycisk *Uruchom* , kliknij go i wybierz polecenie *Uruchom "Azure-Function-egzamin..."* zobaczysz, że aplikacja funkcji działa lokalnie z kilkoma dziennikami.

    ![Projekt funkcji uruchamiania lokalnego](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Dane wyjściowe funkcji uruchamiania lokalnego](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Funkcję można wypróbować, uzyskując dostęp do wydrukowanego punktu końcowego z przeglądarki, na przykład `http://localhost:7071/api/HttpTrigger-Java?name=Azure` .

    ![Wynik testu lokalnych funkcji uruchamiania](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Dziennik jest również drukowany na Twoim POMYSŁie, a teraz Zatrzymaj funkcję, klikając przycisk *Zatrzymaj* .

    ![Dziennik testu funkcji uruchamiania lokalnego](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Lokalne debugowanie aplikacja funkcji

1. Teraz Spróbujmy debugować aplikacja funkcji lokalnie, klikając przycisk *Debuguj* na pasku narzędzi (jeśli go nie widzisz, kliknij przycisk *Wyświetl-> wygląd-> pasku* narzędzi, aby włączyć pasek narzędzi).

    ![Przycisk funkcji debugowania lokalnego](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Kliknij wiersz *20* pliku `src/main/java/org/example/functions/HttpTriggerFunction.java` , aby dodać punkt przerwania, ponownie uzyskaj dostęp do punktu końcowego `http://localhost:7071/api/HttpTrigger-Java?name=Azure` , aby znaleźć punkt przerwania, możesz wypróbować więcej funkcji debugowania, takich jak *krok*, *czujka*, *Ocena*. Zatrzymaj sesję debugowania, klikając przycisk Zatrzymaj.

    ![Podział lokalnych funkcji debugowania](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Wdrażanie aplikacja funkcji na platformie Azure

1. Kliknij prawym przyciskiem myszy projekt w IntelliJ Project Explorer, wybierz pozycję *Azure-> Wdróż do Azure Functions*

    ![Wdrażanie funkcji na platformie Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Jeśli nie masz jeszcze żadnych aplikacja funkcji, kliknij przycisk *nie dostępna funkcja, a następnie kliknij, aby utworzyć nowy*.

    ![Wdrażanie funkcji w usłudze Azure Create App](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Wpisz nazwę aplikacji funkcji i wybierz odpowiednią subskrypcję/platformę/grupę zasobów/App Service plan, a następnie w tym miejscu możesz utworzyć plan grupy zasobów/App Service. Następnie pozostaw ustawienia aplikacji bez zmian, kliknij przycisk *OK* i poczekaj kilka minut na utworzenie nowej funkcji. Po *utworzeniu nowego aplikacja funkcji...* pasek postępu znika.

    ![Kreator wdrażania funkcji w usłudze Azure Create App](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Wybierz aplikację funkcji, w której chcesz wdrożyć program (nowo utworzona aplikacja funkcji zostanie automatycznie zaznaczona). Kliknij przycisk *Uruchom* , aby wdrożyć swoje funkcje.

    ![Wdrażanie funkcji w usłudze Azure Run](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Wdróż funkcje w usłudze Azure log](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Zarządzaj Azure Functions z POMYSŁu

1. Możesz zarządzać funkcjami przy użyciu programu *Azure Explorer* w Twoim pomysłie, klikając pozycję *aplikacja funkcji*, zobaczysz wszystkie funkcje w tym miejscu.

    ![Wyświetl funkcje w Eksploratorze](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Kliknij, aby wybrać jedną z funkcji, a następnie kliknij prawym przyciskiem myszy pozycję *Pokaż właściwości* , aby otworzyć stronę szczegółów. 

    ![Pokaż właściwości funkcji](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Kliknij prawym przyciskiem myszy funkcję *HttpTrigger-Java*, a następnie wybierz pozycję *wyzwalacz funkcja*, zobaczysz, że przeglądarka zostanie otwarta z adresem URL wyzwalacza.

    ![Wdrażanie funkcji w usłudze Azure Run](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Dodaj więcej funkcji do projektu

1. Kliknij prawym przyciskiem myszy pakiet Package *. example. Functions* i wybierz polecenie *New-> klasy funkcji platformy Azure*. 

    ![Dodawanie funkcji do wpisu projektu](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Wypełnij pola Nazwa klasy *HttpTest* i wybierz pozycję *HttpTrigger* w Kreatorze tworzenia klasy funkcji, a następnie kliknij przycisk *OK* , aby utworzyć, w ten sposób można utworzyć nowe funkcje zgodnie z potrzebami.

    ![Dodawanie funkcji do projektu wybierz wyzwalacz](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Dodawanie funkcji do danych wyjściowych projektu](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Czyszczenie funkcji

1. Usuwanie Azure Functions w Eksploratorze Azure
      
      ![Dodawanie funkcji do projektu wybierz wyzwalacz](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Następne kroki

Utworzono projekt funkcji języka Java z funkcją wyzwalaną przez protokół HTTP, uruchom go na komputerze lokalnym i wdrożony na platformie Azure. Teraz możesz rozłożyć funkcję przez...

> [!div class="nextstepaction"]
> [Dodawanie powiązania danych wyjściowych kolejki usługi Azure Storage](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png