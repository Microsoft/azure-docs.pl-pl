---
title: 'Szybki start: tworzenie pierwszej statycznej aplikacji internetowej za pomocą Azure Static Web Apps użyciu Azure Portal'
description: Dowiedz się, jak wdrożyć statyczną witrynę Azure Static Web Apps użyciu Azure Portal.
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 09/03/2020
ms.topic: quickstart
ms.service: static-web-apps
ms.custom:
- mode-portal
ms.openlocfilehash: 78af2290a2e71d349303d3913f8a40510eb9c6a0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531586"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>Szybki start: tworzenie pierwszej statycznej witryny w Azure Portal

Azure Static Web Apps publikuje witrynę internetową w środowisku produkcyjnym przez tworzenie aplikacji z repozytorium GitHub. W tym przewodniku Szybki start wdrożysz aplikację internetową w usłudze Azure Static Web Apps przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, utwórz [konto bezpłatnej wersji próbnej.](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi [GitHub](https://github.com)
- [Konto platformy Azure](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

Po utworzeniu repozytorium możesz utworzyć statyczną aplikację internetową na podstawie Azure Portal.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Wybieranie **opcji Utwórz zasób**
1. Wyszukaj usługę **Static Web Apps**
1. Wybierz **Static Web Apps (wersja zapoznawcza)**
1. Wybierz pozycję **Utwórz**

W sekcji _Podstawy rozpocznij_ od skonfigurowania nowej aplikacji i połączenia jej z repozytorium GitHub.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Sekcja Podstawowe":::

1. Wybierz _subskrypcję platformy Azure_
1. Wybierz lub utwórz nową _grupę zasobów_
1. Nadaj aplikacji **nazwę my-first-static-web-app.**
      1. Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`.
1. Wybierz _region najbliżej_ Ciebie
1. Wybieranie **bezpłatnej sku** 
1. Wybierz przycisk **Sign-in with GitHub (Zaloguj się przy użyciu usługi GitHub)** i uwierzytelnij się w usłudze GitHub

Po zalogowaniu się w usłudze GitHub wprowadź informacje o repozytorium.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Szczegóły repozytorium":::

1. Wybierz preferowaną _organizację_
1. Wybierz **pozycję my-first-web-static-app** z _listy rozwijanej_ Repozytorium
1. Wybierz **pozycję main** z listy _rozwijanej_ Branch (Gałąź)

> [!NOTE]
> Jeśli nie widzisz żadnych repozytoriów, może być konieczne autoryzowanie Azure Static Web Apps usłudze GitHub. Przejdź do repozytorium GitHub i przejdź do > Settings > Applications > Authorized OAuth Apps (Ustawienia aplikacji **> autoryzowanych aplikacji OAuth),** wybierz pozycję Azure Static Web Apps , **a** następnie wybierz pozycję **Grant (Ułań).** W przypadku repozytoriów organizacji musisz być właścicielem organizacji, aby udzielić uprawnień.

1. W sekcji _Szczegóły kompilacji_ dodaj szczegóły konfiguracji specyficzne dla preferowanej struktury frontonie.

    # <a name="no-framework"></a>[Brak struktury](#tab/vanilla-javascript)

    1. Wybierz **pozycję Niestandardowy** z listy _rozwijanej Ustawienia wstępne kompilacji_
    1. Zachowaj wartość domyślną w _polu Lokalizacja_ aplikacji
    1. Wyczyść wartość domyślną z pola _Lokalizacja interfejsu_ API
    1. Pozostaw _puste pole Lokalizacja artefaktu_ aplikacji

    # <a name="angular"></a>[Angular](#tab/angular)

    1. Wybierz **pozycję Angular** z _listy rozwijanej Ustawienia wstępne kompilacji_
    1. Zachowaj wartość domyślną w _polu Lokalizacja_ aplikacji
    1. Wyczyść wartość domyślną z pola _Lokalizacja interfejsu_ API
    1. Wpisz **dist/angular-basic** w _polu Lokalizacja artefaktu_ aplikacji

    # <a name="react"></a>[React](#tab/react)

    1. Wybierz **pozycję React** z listy _rozwijanej Ustawienia wstępne kompilacji_
    1. Zachowaj wartość domyślną w _polu Lokalizacja_ aplikacji
    1. Wyczyść wartość domyślną z pola _Lokalizacja interfejsu_ API
    1. Wpisz **build (kompilacja)** _w polu Lokalizacja artefaktu_ aplikacji

    # <a name="vue"></a>[Vue](#tab/vue)

    1. Wybierz **Vue.js** z listy _rozwijanej Build Presets (Ustawienia wstępne kompilacji)_
    1. Zachowaj wartość domyślną w _polu Lokalizacja_ aplikacji
    1. Wyczyść wartość domyślną z pola _Lokalizacja interfejsu_ API
    1. Zachowaj wartość domyślną w polu _Lokalizacja artefaktu_ aplikacji

    ---

1. Wybierz pozycję **Przejrzyj i utwórz**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Przycisk Przejrzyj tworzenie":::

    > [!NOTE]
    > Możesz edytować plik [przepływu pracy,](github-actions-workflow.md) aby zmienić te wartości po utworzeniu aplikacji.

1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Przycisk Utwórz":::

1. Wybierz pozycję **Przejdź do zasobu**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Przycisk Przejdź do zasobu":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, możesz usunąć wystąpienie Azure Static Web Apps w następujący sposób:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie aplikacji **my-first-web-static-app** na górnym pasku wyszukiwania
1. Wybierz nazwę aplikacji
1. Wybieranie **przycisku** Usuń
1. Wybierz **pozycję Tak,** aby potwierdzić akcję usuwania (ukończenie tej akcji może potrwać kilka minut)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie interfejsu API](add-api.md)
