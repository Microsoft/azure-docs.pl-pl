---
title: 'Szybki Start: Tworzenie pierwszej statycznej aplikacji sieci Web przy użyciu statycznej Web Apps platformy Azure'
description: Dowiedz się, jak utworzyć witrynę sieci Web Web Apps statycznej platformy Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: db3836e6171d187539b8615efcb5ab782c368020
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88752403"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Szybki Start: Tworzenie pierwszej statycznej aplikacji sieci Web

Usługa Azure static Web Apps publikuje witrynę sieci Web w środowisku produkcyjnym, tworząc aplikacje z repozytorium GitHub. W tym przewodniku szybki start wdrożono aplikację sieci Web w usłudze Azure static Web Apps przy użyciu rozszerzenia Visual Studio Code.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi [GitHub](https://github.com)
- Konto [platformy Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Rozszerzenie Web Apps statycznej platformy Azure dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Następnie otwórz Visual Studio Code i przejdź do **pliku > Otwórz folder** , aby otworzyć repozytorium, które zostało właśnie sklonowane do maszyny w edytorze.

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

1. W programie Visual Studio Code na pasku Activity Bar wybierz logo platformy Azure, aby otworzyć okno rozszerzenia platformy Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo platformy Azure":::

    > [!NOTE]
    > Wymagane jest zalogowanie się do platformy Azure i usługi GitHub. Jeśli jeszcze nie zalogowano się do platformy Azure ani usługi GitHub z poziomu programu Visual Studio Code, rozszerzenie wyświetli monit o zalogowanie się do nich podczas procesu tworzenia aplikacji.

1. Umieść kursor myszy na etykiecie usługi _Static Web Apps_ i wybierz **znak plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Logo platformy Azure":::

1. W górnej części edytora zostanie otwarte polecenie palate i zostanie wyświetlony komunikat z prośbą o nazwę aplikacji.

    Wpisz ciąg **my-first-static-web-app** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Logo platformy Azure":::

1. Wybierz gałąź **główną** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Logo platformy Azure":::

1. Wybierz **/** jako lokalizację kodu aplikacji, a następnie naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Logo platformy Azure":::

1. Rozszerzenie wyszukuje lokalizację interfejsu API w aplikacji. Ten artykuł nie omawia implementowania interfejsu API.

    Wybierz pozycję **Pomiń teraz** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Logo platformy Azure":::

1. Wybierz lokalizację, w której będą kompilowane pliki dla aplikacji produkcyjnej.

    # <a name="no-framework"></a>[Brak struktury](#tab/vanilla-javascript)

    Wyczyść pole i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Logo platformy Azure":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Wpisz **dist/kątowy — podstawowy** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Logo platformy Azure":::

    # <a name="react"></a>[React](#tab/react)

    Wpisz **build** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Logo platformy Azure":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Wpisz **dist** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Logo platformy Azure":::

    ---

1. Wybierz najbliższą lokalizację i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Logo platformy Azure":::

1. Po utworzeniu aplikacji w programie Visual Studio Code zostanie wyświetlone powiadomienie zawierające potwierdzenie.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Logo platformy Azure":::

1. W oknie Eksploratora Visual Studio Code Wróć do sekcji _statyczne Web Apps_ i kliknij prawym przyciskiem myszy pozycję **produkcja** i wybierz polecenie **Otwórz w portalu** , aby wyświetlić aplikację w Azure Portal.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Logo platformy Azure":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, możesz usunąć wystąpienie Web Apps statycznej platformy Azure za pomocą rozszerzenia.

W oknie Eksploratora Visual Studio Code Wróć do sekcji _statyczne Web Apps_ i kliknij prawym przyciskiem myszy pozycję **My-static-Web-App** , a następnie wybierz polecenie **Usuń**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Logo platformy Azure":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie interfejsu API](add-api.md)
