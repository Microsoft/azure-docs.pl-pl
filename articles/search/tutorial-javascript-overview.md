---
title: 'Samouczek JavaScript: Omówienie integracji wyszukiwania'
titleSuffix: Azure Cognitive Search
description: Omówienie techniczne i konfiguracja dodawania wyszukiwania do witryny sieci Web i wdrażania jej w statycznej aplikacji internetowej platformy Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726883"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1 — Omówienie dodawania wyszukiwania do witryny sieci Web

W tym samouczku przedstawiono witrynę sieci Web, która umożliwia przeszukanie katalogu książek, a następnie wdrożenie witryny sieci Web w statycznej aplikacji internetowej platformy Azure. 

Aplikacja jest dostępna: 
* [Przykład](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Demonstracyjna witryna sieci Web — aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>Co robi przykład? 

Ta przykładowa witryna sieci Web zapewnia dostęp do wykazu książek 10 000. Użytkownik może przeszukać katalog, wprowadzając tekst na pasku wyszukiwania. Gdy użytkownik wprowadza tekst, witryna internetowa użyje funkcji Sugeruj indeks wyszukiwania, aby zakończyć tekst. Po zakończeniu zapytania zostanie wyświetlona lista książek z częścią szczegółów. Użytkownik może wybrać książkę, aby wyświetlić wszystkie szczegóły przechowywane w indeksie wyszukiwania książki. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Ta przykładowa witryna sieci Web zapewnia dostęp do wykazu książek 10 000. Użytkownik może przeszukać katalog, wprowadzając tekst na pasku wyszukiwania. Gdy użytkownik wprowadza tekst, witryna internetowa użyje funkcji Sugeruj indeks wyszukiwania, aby zakończyć tekst. Po zakończeniu wyszukiwania lista książek zostanie wyświetlona z częścią szczegółów. Użytkownik może wybrać książkę, aby wyświetlić wszystkie szczegóły przechowywane w indeksie wyszukiwania książki.":::

Środowisko wyszukiwania obejmuje: 

* Search — udostępnia funkcję wyszukiwania dla aplikacji.
* Sugeruj — oferuje sugestie, które użytkownik pisze na pasku wyszukiwania.
* Wyszukiwanie dokumentów — wyszukuje dokument według identyfikatora, aby pobrać całą jego zawartość na stronie szczegółów.

## <a name="how-is-the-sample-organized"></a>Jak jest zorganizowana przykład?

[Przykład](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website) obejmuje następujące elementy:

|Aplikacja|Przeznaczenie|GitHub<br>Repozytorium<br>Lokalizacja|
|--|--|--|
|Klient|Zareaguj na aplikację (warstwę prezentacji), aby wyświetlić książki z wyszukiwaniem. Wywołuje aplikację funkcji platformy Azure. |[/search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Serwer|Aplikacja funkcji platformy Azure (warstwa biznesowa) — wywołuje interfejs API usługi Azure Wyszukiwanie poznawcze przy użyciu zestawu SDK języka JavaScript |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Wstawianie zbiorcze|Plik JavaScript w celu utworzenia indeksu i dodania do niego dokumentów.|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Zainstaluj następujące elementy dla lokalnego środowiska deweloperskiego. 

- [Node.js 12 lub 14](https://nodejs.org/en/download)
    - Jeśli na komputerze lokalnym jest zainstalowana inna wersja Node.js, należy rozważyć użycie programu [Node Version Manager](https://github.com/nvm-sh/nvm) (NVM) lub kontenera Docker.  
- [Usługa Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) i następujące rozszerzenia
    - [Azure Resources](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups) (Zasoby platformy Azure)
    - [Azure Wyszukiwanie poznawcze 0.2.0 +](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Statyczna aplikacja internetowa platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- Opcjonalnie:
    - W tym samouczku usługa Azure Function API nie jest uruchamiana lokalnie, ale jeśli zamierzasz uruchomić ją lokalnie, musisz zainstalować [usługi Azure-Functions-Core-Tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) globalnie za pomocą następującego polecenia bash: 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Rozwidlenie i klonowanie przykładu wyszukiwania za pomocą usługi git

Rozwidlenie przykładowego repozytorium ma kluczowe znaczenie dla wdrożenia statycznej aplikacji sieci Web. Aplikacje sieci Web określają akcje kompilacji i zawartość wdrożenia na podstawie własnej lokalizacji rozwidlenia w serwisie GitHub. Wykonanie kodu w statycznej aplikacji sieci Web jest zdalne, a usługa Azure static Web Apps odczytuje kod w próbce rozwidlenia.

1. W witrynie GitHub Utwórz rozwidlenie [przykładowego repozytorium](https://github.com/Azure-Samples/azure-search-javascript-samples). 

    Ukończ proces rozwidlenia w przeglądarce sieci Web przy użyciu konta usługi GitHub. Ten samouczek używa Twojego rozwidlenia w ramach wdrożenia do statycznej aplikacji sieci Web platformy Azure. 

1. W terminalu bash Pobierz przykładową aplikację na komputer lokalny. 

    Zamień `YOUR-GITHUB-ALIAS` na alias usługi GitHub. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. W Visual Studio Code Otwórz lokalny folder sklonowanego repozytorium. Pozostałe zadania są wykonywane z Visual Studio Code, chyba że zostaną określone.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Tworzenie grupy zasobów dla zasobów platformy Azure

1. W Visual Studio Code Otwórz [Pasek aktywności](https://code.visualstudio.com/docs/getstarted/userinterface), a następnie wybierz ikonę platformy Azure. 
1. Na pasku bocznym **kliknij prawym przyciskiem myszy subskrypcję platformy Azure** w obszarze, `Resource Groups` a następnie wybierz pozycję **Utwórz grupę zasobów**.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="Na pasku bocznym * * kliknij prawym przyciskiem myszy subskrypcję platformy Azure * * w obszarze grupy zasobów, a następnie wybierz pozycję * * Utwórz grupę zasobów * *.":::
1. Wprowadź nazwę grupy zasobów, na przykład `cognitive-search-website-tutorial` . 
1. Wybierz lokalizację bliską.
1. Gdy tworzysz Wyszukiwanie poznawcze i statyczne zasoby aplikacji sieci Web, w dalszej części tego samouczka Użyj tej grupy zasobów. 

    Utworzenie grupy zasobów umożliwia jednostce logicznej zarządzanie zasobami, w tym usuwanie ich po zakończeniu korzystania z nich.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie indeksu wyszukiwania i ładowanie ich przy użyciu dokumentów](tutorial-javascript-create-load-index.md)
* [Wdrażanie statycznej aplikacji sieci Web](tutorial-javascript-deploy-static-web-app.md)
