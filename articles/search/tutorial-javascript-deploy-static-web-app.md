---
title: 'Samouczek JavaScript: wdrażanie witryny sieci Web z obsługą wyszukiwania'
titleSuffix: Azure Cognitive Search
description: Wdróż witrynę internetową z obsługą wyszukiwania w statycznej aplikacji sieci Web platformy Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726895"
---
# <a name="3---deploy-the-search-enabled-website"></a>3 — wdrażanie witryny sieci Web z obsługą wyszukiwania

Wdróż witrynę sieci Web z obsługą wyszukiwania jako statyczną aplikację sieci Web platformy Azure. To wdrożenie obejmuje aplikację do reagowania i aplikację funkcji.  

Statyczna aplikacja internetowa pobiera informacje i pliki do wdrożenia z usługi GitHub za pomocą rozwidlenia repozytorium przykładów.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Tworzenie statycznej aplikacji sieci Web w Visual Studio Code

1. Na pasku działania wybierz pozycję **Azure** , a następnie wybierz pozycję **statyczne Web Apps** na pasku bocznym. 
1. Kliknij prawym przyciskiem myszy nazwę subskrypcji, a następnie wybierz pozycję **Utwórz statyczną aplikację internetową (zaawansowaną)**.    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Kliknij prawym przyciskiem myszy nazwę subskrypcji, a następnie wybierz pozycję * * Utwórz statyczną aplikację sieci Web (Zaawansowane) * *.":::

1. Postępuj zgodnie z monitami, aby podać następujące informacje:

    |Monit|Enter|
    |--|--|
    |Jak chcesz utworzyć statyczną aplikację sieci Web?|Użyj istniejącego repozytorium GitHub|
    |Wybierz organizację|Wybierz _własny_ alias usługi GitHub jako organizację.|
    |Wybieranie repozytorium|Wybierz z listy pozycję **Azure-Search-JavaScript-Samples** . |
    |Wybierz gałąź repozytorium|Z listy wybierz pozycję **Master** . |
    |Wprowadź nazwę nowej statycznej aplikacji sieci Web.|Utwórz unikatową nazwę dla zasobu. Na przykład możesz połączyć swoją nazwę z nazwą repozytorium, taką jak, `joansmith-azure-search-javascript-samples` . |
    |Wybierz grupę zasobów dla nowych zasobów.|Użyj grupy zasobów utworzonej dla tego samouczka.|
    |Wybierz opcję kompilacja wstępnie ustawiona, aby skonfigurować domyślną strukturę projektu.|Wybierz **niestandardowe**|
    |Wybierz lokalizację kodu aplikacji|`search-website`|
    |Wybierz lokalizację kodu funkcji platformy Azure|`search-website/api`|
    |Wprowadź ścieżkę do danych wyjściowych kompilacji...|kompilacja|
    |Wybierz lokalizację dla nowych zasobów.|Wybierz region w pobliżu.|

1. Zasób zostanie utworzony, a następnie wybierz pozycję **Otwórz akcje w witrynie GitHub** na stronie powiadomienia. Spowoduje to otwarcie okna przeglądarki wskazywanego przez Twoje repozytorium rozwidlenia. 

    Lista akcji wskazuje, że aplikacja sieci Web, zarówno klienta, jak i funkcje, zostały pomyślnie wypchnięte do statycznej aplikacji sieci Web platformy Azure. 

    Przed kontynuowaniem poczekaj na zakończenie kompilacji i wdrożenia. Może to potrwać minutę lub dwa.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Pobierz klucz zapytania Wyszukiwanie poznawcze w Visual Studio Code

1. W Visual Studio Code Otwórz [Pasek aktywności](https://code.visualstudio.com/docs/getstarted/userinterface), a następnie wybierz ikonę platformy Azure. 

1. Na pasku bocznym wybierz subskrypcję platformy Azure w obszarze **Azure: wyszukiwanie poznawcze** , a następnie kliknij prawym przyciskiem myszy zasób wyszukiwania i wybierz polecenie **Kopiuj klucz zapytania**. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="Na pasku bocznym wybierz swoją subskrypcję platformy Azure w obszarze * * Azure: Wyszukiwanie poznawcze * *, a następnie kliknij prawym przyciskiem myszy zasób wyszukiwania i wybierz pozycję * * Kopiuj klucz zapytania * *.":::

1. Zachowaj ten klucz zapytania, musisz go użyć w następnej sekcji. Klucz zapytania jest w stanie zbadać indeks. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Dodawanie ustawień konfiguracji w Visual Studio Code

Aplikacja funkcji platformy Azure nie zwróci danych wyszukiwania, dopóki wpisy tajne wyszukiwania nie znajdują się w ustawieniach. 

1. Na pasku działania wybierz pozycję **Azure** , a następnie wybierz pozycję **statyczne Web Apps** na pasku bocznym. 
1. Rozszerz nową statyczną aplikację sieci Web do momentu wyświetlenia **ustawień aplikacji** .
1. Kliknij prawym przyciskiem myszy pozycję **Ustawienia aplikacji**, a następnie wybierz pozycję **Dodaj nowe ustawienie**.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="Kliknij prawym przyciskiem myszy pozycję * * Ustawienia aplikacji * *, a następnie wybierz pozycję * * Dodaj nowe ustawienie * *.":::

1. Dodaj następujące ustawienia:

    |Ustawienie|Wartość zasobu wyszukiwania|
    |--|--|
    |SearchApiKey|Klucz zapytania wyszukiwania|
    |SearchServiceName|Nazwa zasobu wyszukiwania|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Korzystanie z wyszukiwania w statycznej aplikacji sieci Web

1. W Visual Studio Code Otwórz [Pasek aktywności](https://code.visualstudio.com/docs/getstarted/userinterface), a następnie wybierz ikonę platformy Azure.
1. Na pasku bocznym **kliknij prawym przyciskiem myszy subskrypcję platformy Azure** w obszarze `Static web apps` i Znajdź statyczną aplikację sieci Web utworzoną dla tego samouczka.
1. Kliknij prawym przyciskiem myszy nazwę statycznej aplikacji sieci Web i wybierz polecenie **Przeglądaj witrynę**.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Kliknij prawym przyciskiem myszy nazwę statycznej aplikacji sieci Web i wybierz pozycję * * Przeglądaj witrynę * *.":::    

1. Wybierz pozycję **Otwórz** w wyskakującym oknie dialogowym.
1. Na pasku wyszukiwania witryny sieci Web wprowadź zapytanie wyszukiwania, takie jak `code` , _wolno_ , aby funkcja Sugeruj sugerowała tytuły książek. Wybierz sugestię lub Kontynuuj wprowadzanie własnych zapytań. Naciśnij klawisz Enter po zakończeniu zapytania wyszukiwania. 
1. Przejrzyj wyniki, a następnie wybierz jedną z książek, aby wyświetlić więcej szczegółów. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, Usuń grupę zasobów.

1. W Visual Studio Code Otwórz [Pasek aktywności](https://code.visualstudio.com/docs/getstarted/userinterface), a następnie wybierz ikonę platformy Azure. 

1. Na pasku bocznym **kliknij prawym przyciskiem myszy subskrypcję platformy Azure** w obszarze `Resource Groups` i Znajdź grupę zasobów utworzoną dla tego samouczka.
1. Kliknij prawym przyciskiem myszy nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.
    Spowoduje to usunięcie zarówno zasobów wyszukiwania, jak i statycznej aplikacji sieci Web.
1. Jeśli nie potrzebujesz już rozwidlenia GitHub przykładu, pamiętaj, aby usunąć go z usługi GitHub. Przejdź do **ustawień** rozwidlenia, a następnie usuń rozwidlenie. 


## <a name="next-steps"></a>Następne kroki

* [Omówienie integracji wyszukiwania dla witryny sieci Web z włączoną funkcją wyszukiwania](tutorial-javascript-search-query-integration.md)
