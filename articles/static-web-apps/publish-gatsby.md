---
title: 'Samouczek: publikowanie witryny Gatsby w usłudze Azure static Web Apps'
description: W tym samouczku pokazano, jak wdrożyć aplikację Gatsby w usłudze Azure static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4430ed34858077b13b4fec69756c1c7e9f3ef7ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652376"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Samouczek: publikowanie witryny Gatsby w wersji zapoznawczej usługi Azure static Web Apps

W tym artykule pokazano, jak utworzyć i wdrożyć aplikację sieci Web [Gatsby](https://gatsbyjs.org) w usłudze [Azure static Web Apps](overview.md). Końcowym wynikiem jest Nowa lokacja statyczna Web Apps (ze skojarzonymi akcjami GitHub), która zapewnia kontrolę nad sposobem kompilowania i publikowania aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie aplikacji Gatsby
> - Konfigurowanie lokacji statycznej Web Apps platformy Azure
> - Wdrażanie aplikacji Gatsby na platformie Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://azure.microsoft.com/free/).
- Konto usługi GitHub. Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://github.com/join).
- [Node.js](https://nodejs.org) zainstalowane.

## <a name="create-a-gatsby-app"></a>Tworzenie aplikacji Gatsby

Tworzenie aplikacji Gatsby przy użyciu interfejsu wiersza polecenia Gatsby (CLI):

1. Otwórz Terminal
1. Użyj narzędzia [npx](https://www.npmjs.com/package/npx) , aby utworzyć nową aplikację przy użyciu interfejsu wiersza polecenia Gatsby. Może to potrwać kilka minut.

   ```bash
   npx gatsby new static-web-app
   ```

1. Przejdź do nowo utworzonej aplikacji

   ```bash
   cd static-web-app
   ```

1. Inicjowanie repozytorium git

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Wypychanie aplikacji do usługi GitHub

Musisz mieć repozytorium w usłudze GitHub, aby utworzyć nowy zasób usługi Azure static Web Apps.

1. Utwórz puste repozytorium GitHub (nie twórz pliku Readme) z [https://github.com/new](https://github.com/new) nazwanego **Gatsby-static-Web-App**.

1. Następnie Dodaj utworzone repozytorium GitHub jako zdalne do lokalnego repozytorium. Pamiętaj, aby dodać nazwę użytkownika usługi GitHub zamiast `<YOUR_USER_NAME>` symbolu zastępczego w poniższym poleceniu.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. Wypychanie lokalnego repozytorium do serwisu GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Wdrażanie aplikacji sieci Web

Poniższe kroki pokazują, jak utworzyć nową aplikację ze statyczną lokacją i wdrożyć ją w środowisku produkcyjnym.

### <a name="create-the-application"></a>Tworzenie aplikacji

1. Przejdź do [Azure Portal](https://portal.azure.com)
1. Kliknij pozycję **Utwórz zasób**
1. Wyszukaj usługę **Static Web Apps**
1. Kliknij pozycję **Static Web Apps (wersja zapoznawcza)**
1. Kliknij pozycję **Utwórz**

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Tworzenie statycznej Web Apps (wersja zapoznawcza) w portalu":::

1. W obszarze _subskrypcja_ Zaakceptuj subskrypcję wymienioną na liście lub wybierz nową z listy rozwijanej.

1. W obszarze _Grupa zasobów_ wybierz pozycję **Nowy**. W polu _Nazwa nowej grupy zasobów_ wprowadź **Gatsby-static-Web-App** , a następnie wybierz **przycisk OK**.

1. Następnie wpisz nazwę aplikacji w polu **Nazwa** . Prawidłowe znaki to `a-z` , `A-Z` `0-9` i `-` .

1. W _obszarze region_ wybierz dostępny region blisko siebie.

1. W obszarze _jednostka SKU_ wybierz pozycję **bezpłatnie**.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Szczegóły wypełnione":::

1. Kliknij przycisk **Zaloguj się przy użyciu usługi GitHub** .

1. Wybierz **organizację** , w której utworzono repozytorium.

1. Wybierz pozycję **Gatsby-static-Web-App** jako _repozytorium_ .

1. Dla _gałęzi_ wybierz pozycję **Main**.

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Zakończono informacje z serwisu GitHub":::

### <a name="build"></a>Kompilacja

Następnie Dodaj ustawienia konfiguracji, które są używane przez proces kompilacji do kompilowania aplikacji.

1. Kliknij przycisk **Dalej: Skompiluj >**, aby edytować konfigurację kompilacji

1. Aby skonfigurować ustawienia kroku w akcjach usługi GitHub, należy ustawić _lokalizację aplikacji_ na **/** .

1. Ustaw _lokalizację artefaktu aplikacji_ na **publiczną**.

   Wartość _lokalizacji interfejsu API_ nie jest konieczna, ponieważ w tej chwili nie jest WDRAŻANY interfejs API.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Ustawienia kompilacji":::

### <a name="review-and-create"></a>Przegląd i tworzenie

1. Kliknij przycisk **Recenzja + Utwórz** , aby sprawdzić, czy szczegóły są poprawne.

1. Kliknij przycisk **Utwórz** , aby rozpocząć tworzenie App Service statycznej aplikacji sieci Web i aprowizacji akcji usługi GitHub na potrzeby wdrożenia.

1. Po zakończeniu wdrażania kliknij pozycję **zasób**.

1. Na ekranie zasób kliknij link _adresu URL_ , aby otworzyć wdrożoną aplikację. Może być konieczne poczekanie minutę lub dwie, aby wykonać akcję usługi GitHub.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Wdrożona aplikacja":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](custom-domain.md)
