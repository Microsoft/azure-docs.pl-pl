---
title: 'Szybki Start: Tworzenie pierwszej statycznej aplikacji sieci Web za pomocą usługi Azure static Web Apps przy użyciu Azure Portal'
description: Dowiedz się, jak utworzyć wystąpienie Web Apps statycznej platformy Azure przy użyciu Azure Portal.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: e0b78c5e053c5668fbebd8ebaac91a90aa2b364f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752902"
---
# <a name="quickstart-building-your-first-static-web-app-in-the-azure-portal"></a>Szybki Start: Tworzenie pierwszej statycznej aplikacji sieci Web w Azure Portal

Usługa Azure static Web Apps publikuje witrynę sieci Web w środowisku produkcyjnym, tworząc aplikacje z repozytorium GitHub. W tym przewodniku szybki start wdrożono aplikację sieci Web w usłudze Azure static Web Apps przy użyciu portalu.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi [GitHub](https://github.com)
- Konto [platformy Azure](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji sieci Web

Po utworzeniu repozytorium można utworzyć statyczną aplikację sieci Web na podstawie Azure Portal.

1. Przejdź do [Azure Portal](https://portal.azure.com)
1. Wybierz pozycję **Utwórz zasób**
1. Wyszukaj usługę **Static Web Apps**
1. Wybierz **Web Apps statyczny (wersja zapoznawcza)**
1. Wybierz pozycję **Utwórz**

Na karcie _podstawowe_ Zacznij od skonfigurowania nowej aplikacji i powiązania jej z repozytorium GitHub.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Karta Podstawowe":::

1. Wybierz swoją _subskrypcję platformy Azure_
1. Wybierz lub Utwórz nową _grupę zasobów_
1. Nadaj aplikacji nazwę **My-static-Web-App**.
      1. Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`.
1. Wybierz _region_ znajdujący się najbliżej siebie
1. Wybierz **bezpłatną** _jednostkę SKU_
1. Wybierz przycisk **Zaloguj się przy użyciu usługi GitHub** i Uwierzytelnij się przy użyciu usługi GitHub

Po zalogowaniu się za pomocą usługi GitHub wprowadź informacje o repozytorium.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Szczegóły repozytorium":::

1. Wybierz preferowaną _organizację_
1. Wybierz pozycję **moja-First-Web-static-App** z listy rozwijanej _repozytorium_
1. Wybierz pozycję **główna** z listy rozwijanej _rozgałęzienie_
1. Wybierz przycisk **Dalej: kompiluj >** , aby edytować konfigurację kompilacji

:::image type="content" source="media/getting-started-portal/next-build-button.png" alt-text="Przycisk Następna kompilacja":::

> [!NOTE]
> Jeśli nie widzisz żadnych repozytoriów, może być konieczne autoryzowanie Web Apps statycznej platformy Azure w usłudze GitHub. Przejdź do repozytorium GitHub i przejdź do pozycji **ustawienia > aplikacje > autoryzowane aplikacje OAuth**, wybierz pozycję **statyczne Web Apps platformy Azure**, a następnie wybierz pozycję **Udziel**. W przypadku repozytoriów organizacji musisz być właścicielem organizacji, aby przyznać uprawnienia.

1. Na karcie _kompilacja_ Dodaj szczegóły konfiguracji specyficzne dla preferowanej platformy frontonu.

    # <a name="no-framework"></a>[Brak struktury](#tab/vanilla-javascript)

    - Wyczyść wartość domyślną w polu _Lokalizacja aplikacji_
    - Wyczyść wartość domyślną w polu _Lokalizacja interfejsu API_
    - Wyczyść wartość domyślną w polu _Lokalizacja artefaktu aplikacji_

    # <a name="angular"></a>[Angular](#tab/angular)

    - Wyczyść wartość domyślną w polu _Lokalizacja aplikacji_
    - Wyczyść wartość domyślną w polu _Lokalizacja interfejsu API_
    - Typ **ROZKŁ/kątowy — podstawowa** w polu _Lokalizacja artefaktu aplikacji_

    # <a name="react"></a>[React](#tab/react)

    - Wyczyść wartość domyślną pola _Lokalizacja aplikacji_
    - Wyczyść wartość domyślną w polu _Lokalizacja interfejsu API_
    - Wpisz **kompilację** w polu _Lokalizacja artefaktu aplikacji_

    # <a name="vue"></a>[Vue](#tab/vue)

    - Wyczyść wartość domyślną pola _Lokalizacja aplikacji_
    - Wyczyść wartość domyślną w polu _Lokalizacja interfejsu API_
    - Typ " **ROZKŁ** " w polu _Lokalizacja artefaktu aplikacji_

    ---

1. Wybierz pozycję **Przeglądanie + tworzenie**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Przycisk tworzenia przeglądu":::

    > [!NOTE]
    > [Plik przepływu pracy](github-actions-workflow.md) można edytować, aby zmienić te wartości po utworzeniu aplikacji.

1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Przycisk Utwórz":::

1. Wybierz pozycję **Przejdź do zasobu**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Przycisk Przejdź do zasobu":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, możesz usunąć wystąpienie usługi Azure static Web Apps, wykonując następujące czynności:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Wyszukaj **aplikację My-First-Web-static-** from na górnym pasku wyszukiwania
1. Wybierz nazwę aplikacji
1. Wybierz przycisk **Usuń**
1. Wybierz pozycję **tak** , aby potwierdzić akcję usuwania

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie interfejsu API](add-api.md)
