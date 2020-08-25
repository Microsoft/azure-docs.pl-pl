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
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
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

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji sieci Web

1. W obszarze Visual Studio Code wybierz pozycję logo platformy Azure na pasku działania, aby otworzyć okno rozszerzenia platformy Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo platformy Azure":::

    > [!NOTE]
    > Wymagane są logowanie platformy Azure i usługi GitHub. Jeśli użytkownik nie jest jeszcze zalogowany do platformy Azure i serwisu GitHub z Visual Studio Code, rozszerzenie wyświetli monit o zalogowanie się do obydwu podczas procesu tworzenia.

1. Umieść wskaźnik myszy na etykiecie _Web Apps statycznej_ i wybierz znak **Plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nazwa aplikacji":::

1. W górnej części edytora zostanie otwarte polecenie palate i zostanie wyświetlony komunikat z prośbą o nazwę aplikacji.

    Wpisz **My-First-static-Web-App** , a następnie naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Tworzenie statycznej aplikacji sieci Web":::

1. Wybierz gałąź **główną** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Nazwa gałęzi":::

1. Wybierz **/** jako lokalizację kodu aplikacji, a następnie naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Lokalizacja kodu aplikacji":::

1. Rozszerzenie szuka lokalizacji interfejsu API w aplikacji. Ten artykuł nie implementuje interfejsu API.

    Wybierz pozycję **Pomiń teraz** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Lokalizacja interfejsu API":::

1. Wybierz lokalizację, w której pliki zostały skompilowane dla środowiska produkcyjnego w aplikacji.

    # <a name="no-framework"></a>[Brak struktury](#tab/vanilla-javascript)

    Wyczyść pole i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Ścieżka plików aplikacji":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Wpisz **dist/kątowy — podstawowy** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Ścieżka do plików aplikacji kątowych":::

    # <a name="react"></a>[React](#tab/react)

    Wpisz **kompilacja** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Ścieżka do plików aplikacji":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Wpisz **i** naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Ścieżka plików aplikacji Vue":::

    ---

1. Wybierz najbliższą lokalizację i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Lokalizacja zasobu":::

1. Po utworzeniu aplikacji w Visual Studio Code zostanie wyświetlona powiadomienie o potwierdzeniu.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Utworzono potwierdzenie":::

1. W oknie Eksploratora Visual Studio Code Wróć do sekcji _statyczne Web Apps_ i kliknij prawym przyciskiem myszy pozycję **produkcja** i wybierz polecenie **Otwórz w portalu** , aby wyświetlić aplikację w Azure Portal.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Otwórz Portal":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, możesz usunąć wystąpienie Web Apps statycznej platformy Azure za pomocą rozszerzenia.

W oknie Eksploratora Visual Studio Code Wróć do sekcji _statyczne Web Apps_ i kliknij prawym przyciskiem myszy pozycję **My-static-Web-App** , a następnie wybierz polecenie **Usuń**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Usuń aplikację":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie interfejsu API](add-api.md)
