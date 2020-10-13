---
title: 'Samouczek: publikowanie witryny Hugo w usłudze Azure static Web Apps'
description: Dowiedz się, jak wdrożyć aplikację Hugo w usłudze Azure static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: ff408f114784fa3f0b8fab49521b5ec7ec2be102
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797721"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Samouczek: publikowanie witryny Hugo w wersji zapoznawczej usługi Azure static Web Apps

W tym artykule pokazano, jak utworzyć i wdrożyć aplikację sieci Web [Hugo](https://gohugo.io/) w usłudze [Azure static Web Apps](overview.md). Ostatnim wynikiem jest nowa statyczna aplikacja internetowa platformy Azure ze skojarzonymi akcjami GitHub, która zapewnia kontrolę nad sposobem kompilowania i publikowania aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie aplikacji Hugo
> - Konfigurowanie Web Apps statycznej platformy Azure
> - Wdrażanie aplikacji Hugo na platformie Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://azure.microsoft.com/free/).
- Konto usługi GitHub. Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://github.com/join).

## <a name="create-a-hugo-app"></a>Tworzenie aplikacji Hugo

Tworzenie aplikacji Hugo przy użyciu interfejsu wiersza polecenia Hugo (CLI):

1. Postępuj zgodnie z [przewodnikiem instalacji](https://gohugo.io/getting-started/installing/) usługi Hugo w systemie operacyjnym.

1. Otwórz Terminal

1. Uruchom interfejs wiersza polecenia Hugo, aby utworzyć nową aplikację.

   ```bash
   hugo new site static-app
   ```

1. Przejdź do nowo utworzonej aplikacji.

   ```bash
   cd static-app
   ```

1. Zainicjuj repozytorium git.

   ```bash
    git init
   ```

1. Następnie Dodaj motyw do lokacji, instalując kompozycję jako moduł podrzędny git, a następnie określając ją w pliku konfiguracji Hugo.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Zatwierdź zmiany.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Wypychanie aplikacji do usługi GitHub

Aby nawiązać połączenie z usługą Azure static Web Apps, potrzebujesz repozytorium w usłudze GitHub. Poniższe kroki pokazują, jak utworzyć repozytorium dla witryny.

1. Utwórz puste repozytorium GitHub (nie twórz pliku Readme) z [https://github.com/new](https://github.com/new) nazwanego **Hugo-static-App**.

1. Dodaj repozytorium GitHub jako element zdalny do lokalnego repozytorium. Pamiętaj, aby dodać nazwę użytkownika usługi GitHub zamiast `<YOUR_USER_NAME>` symbolu zastępczego w poniższym poleceniu.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Wypychanie lokalnego repozytorium do serwisu GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Wdrażanie aplikacji sieci Web

Poniższe kroki pokazują, jak utworzyć nową aplikację ze statyczną lokacją i wdrożyć ją w środowisku produkcyjnym.

### <a name="create-the-application"></a>Tworzenie aplikacji

1. Przejdź do [Azure Portal](https://portal.azure.com)
1. Kliknij pozycję **Utwórz zasób**
1. Wyszukaj usługę **Static Web Apps**
1. Kliknij pozycję **Static Web Apps (wersja zapoznawcza)**
1. Kliknij pozycję **Utwórz**

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Tworzenie zasobu usługi Azure static Web Apps w portalu":::

1. W obszarze **subskrypcja**Zaakceptuj subskrypcję wymienioną na liście lub wybierz nową z listy rozwijanej.

1. W obszarze _Grupa zasobów_wybierz pozycję **Nowy**. W polu _Nazwa nowej grupy zasobów_wprowadź **Hugo-static-App** , a następnie wybierz **przycisk OK**.

1. Następnie wpisz nazwę aplikacji w polu **Nazwa** . Prawidłowe znaki to `a-z` , `A-Z` `0-9` i `-` .

1. W _obszarze region_wybierz dostępny region blisko siebie.

1. W obszarze _jednostka SKU_wybierz pozycję **bezpłatnie**.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Tworzenie zasobu usługi Azure static Web Apps w portalu":::

1. Kliknij przycisk **Zaloguj się przy użyciu usługi GitHub** .

1. Wybierz **organizację** , w której utworzono repozytorium.

1. Wybierz **Hugo-static-App** jako _repozytorium_ .

1. Dla _gałęzi_ wybierz gałąź **główna**.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Tworzenie zasobu usługi Azure static Web Apps w portalu":::

### <a name="build"></a>Kompilacja

Następnie Dodaj ustawienia konfiguracji, które są używane przez proces kompilacji do kompilowania aplikacji. Poniższe ustawienia umożliwiają skonfigurowanie pliku przepływu pracy akcji usługi GitHub.

1. Kliknij przycisk **Dalej: Skompiluj >**, aby edytować konfigurację kompilacji

1. Ustaw _lokalizację aplikacji_ na **/** .

1. Ustaw _lokalizację artefaktu aplikacji_ na **publiczną**.

   Wartość _lokalizacji interfejsu API_ nie jest konieczna, ponieważ w tej chwili nie jest WDRAŻANY interfejs API.

### <a name="review-and-create"></a>Przegląd i tworzenie

1. Kliknij przycisk **Recenzja + Utwórz** , aby sprawdzić, czy szczegóły są poprawne.

1. Kliknij przycisk **Utwórz** , aby rozpocząć tworzenie Web Apps statycznej platformy Azure i zainicjować akcję GitHub na potrzeby wdrożenia.

1. Poczekaj na zakończenie akcji usługi GitHub.

1. W oknie _przegląd_ Azure Portal nowo utworzonego zasobu statycznego Web Apps platformy Azure kliknij link _adresu URL_ , aby otworzyć wdrożoną aplikację.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Tworzenie zasobu usługi Azure static Web Apps w portalu":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](custom-domain.md)
