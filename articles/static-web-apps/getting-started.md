---
title: 'Szybki start: tworzenie pierwszej statycznej witryny przy użyciu Azure Static Web Apps'
description: Dowiedz się, jak wdrożyć statyczną witrynę Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: fb874c25ab688cc5e6723d1023157b8acd9478b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483852"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Szybki start: tworzenie pierwszej statycznej witryny przy użyciu Azure Static Web Apps

Azure Static Web Apps publikuje witrynę internetową, budowania aplikacji z repozytorium kodu. W tym przewodniku Szybki start wdrożysz aplikację w usłudze Azure Static Web Apps przy użyciu Visual Studio Code aplikacji.

Jeśli nie masz subskrypcji platformy Azure, utwórz [konto bezpłatnej wersji próbnej.](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi [GitHub](https://github.com)
- [Konto platformy Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Rozszerzenia usługi Azure Static Web Apps dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Zainstaluj oprogramowanie Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Następnie otwórz plik Visual Studio Code i przejdź do > **Otwórz folder,** aby otworzyć repozytorium sklonowane na maszynę w edytorze.

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

1. W programie Visual Studio Code na pasku Activity Bar wybierz logo platformy Azure, aby otworzyć okno rozszerzeń platformy Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo platformy Azure":::

    > [!NOTE]
    > Wymagane jest zalogowanie się do platformy Azure i usługi GitHub. Jeśli jeszcze nie zalogowano się do platformy Azure ani usługi GitHub z poziomu programu Visual Studio Code, rozszerzenie wyświetli monit o zalogowanie się do nich podczas procesu tworzenia aplikacji.

1. Pod _etykietą Static Web Apps_ wybierz **znak plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nazwa aplikacji":::

1. W górnej części edytora zostanie otwarte polecenie i zostanie wyświetlony monit o podanie nazwy aplikacji.

    Wpisz ciąg **my-first-static-web-app** i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Tworzenie statycznej aplikacji internetowej":::

1. Wybierz ustawienia wstępne zgodne z typem aplikacji.

    # <a name="no-framework"></a>[Brak struktury](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Ustawienia wstępne aplikacji: brak struktury":::

    Wprowadź **./** jako lokalizację plików aplikacji.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Lokalizacja plików aplikacji":::

    Wybierz **pozycję Skip for now (Pomiń** teraz) jako lokalizację Azure Functions API.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Lokalizacja interfejsu API":::

    Wprowadź **./** jako lokalizację danych wyjściowych kompilacji.

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Lokalizacja wyjściowa kompilacji aplikacji":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Mimo że istnieje ustawienie wstępne usługi Angular, wybierz opcję **Niestandardowy,** aby udostępnić odpowiednią lokalizację danych wyjściowych dla tej aplikacji.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Ustawienia wstępne aplikacji: Angular":::

    Wprowadź **./** jako lokalizację plików aplikacji.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Lokalizacja plików aplikacji: Angular":::

    Wybierz **pozycję Pomiń** teraz jako lokalizację Azure Functions API.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Lokalizacja interfejsu API: Angular":::

    Wprowadź **dist/angular-basic** jako lokalizację wyjściową kompilacji.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Lokalizacja danych wyjściowych kompilacji aplikacji: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Ustawienia wstępne aplikacji: React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Ustawienia wstępne aplikacji: Vue":::

    ---

1. Wybierz najbliższą lokalizację i naciśnij klawisz **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Lokalizacja zasobu":::

1. Po utworzeniu aplikacji w programie Visual Studio Code zostanie wyświetlone powiadomienie zawierające potwierdzenie.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Utworzone potwierdzenie":::

    Następnie kliknij przycisk Otwórz akcje **w usłudze GitHub.** Na tej stronie przedstawiono stan kompilacji aplikacji.

    Po zakończeniu akcji usługi GitHub możesz przejść do opublikowanej witryny internetowej.

1. Aby wyświetlić witrynę internetową w przeglądarce, kliknij prawym przyciskiem myszy projekt w rozszerzeniu Static Web Apps, a następnie wybierz **pozycję Przeglądaj witrynę.**

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Przeglądanie witryny":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, możesz usunąć wystąpienie Azure Static Web Apps za pomocą rozszerzenia.

W oknie Visual Studio Code Explorer wróć do sekcji _Static Web Apps,_ kliknij prawym przyciskiem myszy pozycję **my-first-static-web-app** i wybierz polecenie **Usuń.**

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Usuwanie aplikacji":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie interfejsu API](add-api.md)
