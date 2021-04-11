---
title: 'Szybki Start: Tworzenie pierwszej witryny statycznej przy użyciu statycznej Web Apps platformy Azure'
description: Dowiedz się, jak wdrożyć lokację statyczną w usłudze Azure static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 335f78bba24947b1b6c3d6132bc38f237b3298b9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449216"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Szybki Start: Tworzenie pierwszej witryny statycznej przy użyciu statycznej Web Apps platformy Azure

Usługa Azure static Web Apps publikuje witrynę sieci Web, tworząc aplikacje z repozytorium kodu. W tym przewodniku szybki start wdrożono aplikację do statycznych aplikacji sieci Web platformy Azure przy użyciu rozszerzenia Visual Studio Code.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi [GitHub](https://github.com)
- Konto [platformy Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Rozszerzenia usługi Azure Static Web Apps dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Zainstaluj oprogramowanie Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Następnie otwórz Visual Studio Code i przejdź do **pliku > Otwórz folder** , aby otworzyć repozytorium sklonowane na maszynie w edytorze.

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

1. W programie Visual Studio Code na pasku Activity Bar wybierz logo platformy Azure, aby otworzyć okno rozszerzeń platformy Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo platformy Azure":::

    > [!NOTE]
    > Wymagane jest zalogowanie się do platformy Azure i usługi GitHub. Jeśli jeszcze nie zalogowano się do platformy Azure ani usługi GitHub z poziomu programu Visual Studio Code, rozszerzenie wyświetli monit o zalogowanie się do nich podczas procesu tworzenia aplikacji.

1. W etykiecie _Web Apps statycznej_ wybierz znak **Plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nazwa aplikacji":::

1. W górnej części edytora zostanie otwarte polecenie palate i zostanie wyświetlony komunikat z prośbą o nazwę aplikacji.

    Wpisz ciąg **my-first-static-web-app** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Tworzenie statycznej aplikacji internetowej":::

1. Wybierz ustawienia wstępne pasujące do typu aplikacji.

    # <a name="no-framework"></a>[Brak struktury](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Ustawienia wstępne aplikacji: brak struktury":::

    Wprowadź **./** jako lokalizację plików aplikacji

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Lokalizacja plików aplikacji":::

    Wybierz pozycję **Pomiń teraz** jako lokalizację interfejsu API Azure Functions

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Lokalizacja interfejsu API":::

    Wprowadź **./** jako lokalizację wyjściową kompilacji

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Lokalizacja wyjściowa kompilacji aplikacji":::

    # <a name="angular"></a>[Angular](#tab/angular)

    :::image type="content" source="media/getting-started/extension-presets-angular.png" alt-text="Ustawienia wstępne aplikacji: kątowy":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Ustawienia wstępne aplikacji: reagowanie":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Ustawienia wstępne aplikacji: Vue":::

    ---

1. Wybierz najbliższą lokalizację i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Lokalizacja zasobu":::

1. Po utworzeniu aplikacji w programie Visual Studio Code zostanie wyświetlone powiadomienie zawierające potwierdzenie.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Utworzone potwierdzenie":::

    Następnie kliknij przycisk **Otwórz akcje w** serwisie GitHub. Na tej stronie jest wyświetlany stan kompilacji aplikacji.

    Po zakończeniu akcji usługi GitHub możesz przejść do opublikowanej witryny sieci Web.

1. Aby wyświetlić witrynę sieci Web w przeglądarce, kliknij prawym przyciskiem myszy projekt w rozszerzeniu Web Apps statycznym, a następnie wybierz polecenie **Przeglądaj witrynę**.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Przeglądaj witrynę":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, możesz usunąć wystąpienie Web Apps statycznej platformy Azure za pomocą rozszerzenia.

W oknie Eksploratora Visual Studio Code Wróć do sekcji _statyczne Web Apps_ i kliknij prawym przyciskiem myszy pozycję **My-static-Web-App** , a następnie wybierz polecenie **Usuń**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Usuń aplikację":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie interfejsu API](add-api.md)
