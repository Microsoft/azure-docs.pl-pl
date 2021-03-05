---
title: 'Szybki Start: Tworzenie pierwszej witryny statycznej przy użyciu statycznej Web Apps platformy Azure'
description: Dowiedz się, jak wdrożyć lokację statyczną w usłudze Azure static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 2d7a2dcbbd0b6e9a10ca8af93798bfddbee94ee3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182659"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Szybki Start: Tworzenie pierwszej witryny statycznej przy użyciu statycznej Web Apps platformy Azure

Usługa Azure static Web Apps publikuje witrynę sieci Web w środowisku produkcyjnym, tworząc aplikacje z repozytorium GitHub. W tym przewodniku szybki start wdrożono aplikację sieci Web w usłudze Azure static Web Apps przy użyciu rozszerzenia Visual Studio Code.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi [GitHub](https://github.com)
- Konto [platformy Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Rozszerzenia usługi Azure Static Web Apps dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Zainstaluj oprogramowanie Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Następnie otwórz Visual Studio Code i przejdź do **pliku > Otwórz folder** , aby otworzyć repozytorium, które zostało właśnie sklonowane do maszyny w edytorze.

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

1. W programie Visual Studio Code na pasku Activity Bar wybierz logo platformy Azure, aby otworzyć okno rozszerzeń platformy Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo platformy Azure":::

    > [!NOTE]
    > Wymagane jest zalogowanie się do platformy Azure i usługi GitHub. Jeśli jeszcze nie zalogowano się do platformy Azure ani usługi GitHub z poziomu programu Visual Studio Code, rozszerzenie wyświetli monit o zalogowanie się do nich podczas procesu tworzenia aplikacji.

1. Umieść kursor myszy na etykiecie usługi _Static Web Apps_ i wybierz **znak plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nazwa aplikacji":::

1. W górnej części edytora zostanie otwarte polecenie palate i zostanie wyświetlony komunikat z prośbą o nazwę aplikacji.

    Wpisz ciąg **my-first-static-web-app** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Tworzenie statycznej aplikacji internetowej":::

1. Wybierz gałąź **main** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Nazwa gałęzi":::

1. Wybierz **/** jako lokalizację kodu aplikacji, a następnie naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Lokalizacja kodu aplikacji":::

1. Rozszerzenie wyszukuje lokalizację interfejsu API w aplikacji. Ten artykuł nie omawia implementowania interfejsu API.

    Wybierz pozycję **Pomiń teraz** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Lokalizacja interfejsu API":::

1. Wybierz lokalizację, w której będą kompilowane pliki do wersji produkcyjnej w aplikacji.

    # <a name="no-framework"></a>[Brak struktury](#tab/vanilla-javascript)

    Wyczyść pole i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Ścieżka plików aplikacji":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Wpisz **dist/kątowy — podstawowy** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Ścieżka plików aplikacji platformy Angular":::

    # <a name="react"></a>[React](#tab/react)

    Wpisz **build** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Ścieżka plików aplikacji platformy React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Wpisz **dist** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Ścieżka plików aplikacji platformy Vue":::

    ---

1. Wybierz najbliższą lokalizację i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Lokalizacja zasobu":::

1. Po utworzeniu aplikacji w programie Visual Studio Code zostanie wyświetlone powiadomienie zawierające potwierdzenie.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Utworzone potwierdzenie":::

1. W oknie Eksplorator Visual Studio Code przejdź do węzła, który ma swoją nazwę subskrypcji, i rozwiń go. Należy pamiętać, że ukończenie wdrożenia może potrwać kilka minut. Następnie wróć do sekcji statyczne Web Apps i wybierz nazwę aplikacji, a następnie kliknij prawym przyciskiem myszy pozycję My-static-Web-App, a następnie wybierz polecenie Otwórz w portalu, aby wyświetlić aplikację w Azure Portal.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Otwórz portal":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, możesz usunąć wystąpienie Web Apps statycznej platformy Azure za pomocą rozszerzenia.

W oknie Eksploratora Visual Studio Code Wróć do sekcji _statyczne Web Apps_ i kliknij prawym przyciskiem myszy pozycję **My-static-Web-App** , a następnie wybierz polecenie **Usuń**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Usuń aplikację":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie interfejsu API](add-api.md)
