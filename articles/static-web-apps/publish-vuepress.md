---
title: 'Samouczek: publikowanie witryny VuePress w usłudze Azure static Web Apps'
description: W tym samouczku pokazano, jak wdrożyć aplikację VuePress w usłudze Azure static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 6f0616df885a7f8fcd76337c810bc368aa02f3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650454"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Samouczek: publikowanie witryny VuePress w wersji zapoznawczej usługi Azure static Web Apps

W tym artykule pokazano, jak utworzyć i wdrożyć aplikację sieci Web [VuePress](https://vuepress.vuejs.org/) w usłudze [Azure static Web Apps](overview.md). Końcowym wynikiem jest nowa aplikacja usługi Azure static Web Apps ze skojarzonymi akcjami GitHub, które zapewniają kontrolę nad sposobem kompilowania i publikowania aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie aplikacji VuePress
> - Konfigurowanie Web Apps statycznej platformy Azure
> - Wdrażanie aplikacji VuePress na platformie Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://azure.microsoft.com/free/).
- Konto usługi GitHub. Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://github.com/join).
- [Node.js](https://nodejs.org) zainstalowane.

## <a name="create-a-vuepress-app"></a>Tworzenie aplikacji VuePress

Tworzenie aplikacji VuePress przy użyciu interfejsu wiersza polecenia (CLI):

1. Utwórz nowy folder dla aplikacji VuePress.

   ```bash
   mkdir static-site
   ```

1. Dodaj plik _README.MD_ do folderu.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Zainicjuj _package.js_ pliku.

   ```bash
   npm init -y
   ```

1. Dodaj VuePress jako `devDependency` .

   ```bash
   npm install --save-dev vuepress
   ```

1. Otwórz _package.jsw_ pliku w edytorze tekstów i Dodaj do sekcji polecenie Build [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) .

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Utwórz plik _. gitignore_ , aby wykluczyć folder _\_ modułów węzła_ .

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Zainicjuj repozytorium git.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Wypychanie aplikacji do usługi GitHub

Aby nawiązać połączenie z usługą Azure static Web Apps, potrzebujesz repozytorium w usłudze GitHub. Poniższe kroki pokazują, jak utworzyć repozytorium dla witryny.

1. Utwórz puste repozytorium GitHub (nie twórz pliku Readme) z [https://github.com/new](https://github.com/new) nazwanego **vuepress-static-App**.

1. Dodaj repozytorium GitHub jako element zdalny do lokalnego repozytorium. Pamiętaj, aby dodać nazwę użytkownika usługi GitHub zamiast `<YOUR_USER_NAME>` symbolu zastępczego w poniższym poleceniu.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Wypychanie lokalnego repozytorium do serwisu GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Wdrażanie aplikacji sieci Web

Poniższe kroki pokazują, jak utworzyć nową aplikację statyczną Web Apps i wdrożyć ją w środowisku produkcyjnym.

### <a name="create-the-application"></a>Tworzenie aplikacji

1. Przejdź do [Azure Portal](https://portal.azure.com)
1. Kliknij pozycję **Utwórz zasób**
1. Wyszukaj usługę **Static Web Apps**
1. Kliknij pozycję **Static Web Apps (wersja zapoznawcza)**
1. Kliknij pozycję **Utwórz**

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Tworzenie statycznej Web Apps (wersja zapoznawcza) w portalu":::

1. W obszarze **subskrypcja** Zaakceptuj subskrypcję wymienioną na liście lub wybierz nową z listy rozwijanej.

1. W obszarze _Grupa zasobów_ wybierz pozycję **Nowy**. W polu _Nazwa nowej grupy zasobów_ wprowadź **vuepress-static-App** , a następnie wybierz **przycisk OK**.

1. Następnie wpisz nazwę aplikacji w polu **Nazwa** . Prawidłowe znaki to `a-z` , `A-Z` `0-9` i `-` .

1. W _obszarze region_ wybierz dostępny region blisko siebie.

1. W obszarze _jednostka SKU_ wybierz pozycję **bezpłatnie**.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Szczegóły wypełnione":::

1. Kliknij przycisk **Zaloguj się przy użyciu usługi GitHub** .

1. Wybierz **organizację** , w której utworzono repozytorium.

1. Wybierz **vuepress-static-App** jako _repozytorium_ .

1. Dla _gałęzi_ wybierz pozycję **Main**.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Zakończono informacje z serwisu GitHub":::

### <a name="build"></a>Kompilacja

Następnie Dodaj ustawienia konfiguracji, które są używane przez proces kompilacji do kompilowania aplikacji. Poniższe ustawienia umożliwiają skonfigurowanie pliku przepływu pracy akcji usługi GitHub.

1. Kliknij przycisk **Dalej: Skompiluj >**, aby edytować konfigurację kompilacji

1. Ustaw _lokalizację aplikacji_ na **/** .

1. Ustaw _lokalizację artefaktu aplikacji_ na **. vuepress/dist**.

Wartość _lokalizacji interfejsu API_ nie jest konieczna, ponieważ w tej chwili nie jest WDRAŻANY interfejs API.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Ustawienia kompilacji":::

### <a name="review-and-create"></a>Przegląd i tworzenie

1. Kliknij przycisk **Recenzja + Utwórz** , aby sprawdzić, czy szczegóły są poprawne.

1. Kliknij przycisk **Utwórz** , aby rozpocząć tworzenie Web Apps statycznej platformy Azure i zainicjować akcję GitHub na potrzeby wdrożenia.

1. Po zakończeniu wdrażania kliknij pozycję **zasób**.

1. Na ekranie zasób kliknij link _adresu URL_ , aby otworzyć wdrożoną aplikację. Może być konieczne poczekanie minutę lub dwie, aby wykonać akcję usługi GitHub.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Wdrożona aplikacja":::

### <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](custom-domain.md)
