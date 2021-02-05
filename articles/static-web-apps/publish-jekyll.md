---
title: 'Samouczek: publikowanie witryny Jekyll w usłudze Azure static Web Apps'
description: Dowiedz się, jak wdrożyć aplikację Jekyll w usłudze Azure static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: 8c6764ad5b63aa2fde07326ab986404ea4312316
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585180"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Samouczek: publikowanie witryny Jekyll w wersji zapoznawczej usługi Azure static Web Apps

W tym artykule pokazano, jak utworzyć i wdrożyć aplikację sieci Web [Jekyll](https://jekyllrb.com/) w usłudze [Azure static Web Apps](overview.md).

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> - Tworzenie witryny sieci Web Jekyll
> - Konfigurowanie Web Apps statycznej platformy Azure
> - Wdrażanie aplikacji Jekyll na platformie Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj [Jekyll](https://jekyllrb.com/docs/installation/)
  - W razie potrzeby można użyć podsystemu Windows dla systemu Linux i postępować zgodnie z instrukcjami Ubuntu.
- Konto platformy Azure z aktywną subskrypcją. Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://azure.microsoft.com/free/).
- Konto usługi GitHub. Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://github.com/join).

## <a name="create-jekyll-app"></a>Tworzenie aplikacji Jekyll

Tworzenie aplikacji Jekyll przy użyciu interfejsu wiersza polecenia Jekyll (CLI):

1. W terminalu uruchom interfejs wiersza polecenia Jekyll, aby utworzyć nową aplikację.

   ```bash
   jekyll new static-app
   ```

1. Przejdź do nowo utworzonej aplikacji.

   ```bash
   cd static-app
   ```

1. Zainicjuj nowe repozytorium git.

   ```bash
    git init
   ```

1. Zatwierdź zmiany.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Wypychanie aplikacji do usługi GitHub

Usługa Azure static Web Apps używa usługi GitHub do publikowania witryny sieci Web. Poniższe kroki pokazują, jak utworzyć repozytorium GitHub.

1. Utwórz puste repozytorium GitHub (nie twórz pliku Readme) z [https://github.com/new](https://github.com/new) nazwanym **Jekyll-Azure-static**.

1. Dodaj repozytorium GitHub jako element zdalny do lokalnego repozytorium. Pamiętaj, aby dodać nazwę użytkownika usługi GitHub zamiast `<YOUR_USER_NAME>` symbolu zastępczego w poniższym poleceniu.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Wypychanie lokalnego repozytorium do serwisu GitHub.

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > Gałąź git może mieć nazwę inną niż `main` . `main`W tym poleceniu Zastąp wartość poprawną wartością.

## <a name="deploy-your-web-app"></a>Wdrażanie aplikacji sieci Web

Poniższe kroki pokazują, jak utworzyć nową aplikację ze statyczną lokacją i wdrożyć ją w środowisku produkcyjnym.

### <a name="create-the-application"></a>Tworzenie aplikacji

1. Przejdź do [Azure Portal](https://portal.azure.com).

1. Kliknij pozycję **Utwórz zasób**.

1. Wyszukaj **statyczne Web Apps**.

1. Kliknij pozycję **statyczne Web Apps (wersja zapoznawcza)**.

1. Kliknij pozycję **Utwórz**.

1. W obszarze **subskrypcja** Zaakceptuj subskrypcję wymienioną na liście lub wybierz nową z listy rozwijanej.

1. W obszarze _Grupa zasobów_ wybierz pozycję **Nowy**. W polu _Nazwa nowej grupy zasobów_ wprowadź **Jekyll-static-App** , a następnie wybierz **przycisk OK**.

1. Następnie podaj nazwę aplikacji w polu _Nazwa_ . Prawidłowe znaki to `a-z` , `A-Z` `0-9` i `-` .

1. W _obszarze region_ wybierz dostępny region blisko siebie.

1. W obszarze _jednostka SKU_ wybierz pozycję **bezpłatnie**.

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="Szczegóły wypełnione":::

1. Kliknij przycisk **Zaloguj się przy użyciu usługi GitHub** .

1. Wybierz **organizację** , w której utworzono repozytorium.

1. Wybierz **Jekyll-static-App** jako _repozytorium_.

1. Dla _gałęzi_ wybierz pozycję **Main**.

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="Zakończono informacje z serwisu GitHub":::

### <a name="build"></a>Kompilacja

Następnie Dodaj ustawienia konfiguracji, które są używane przez proces kompilacji do kompilowania aplikacji. Poniższe ustawienia umożliwiają skonfigurowanie pliku przepływu pracy akcji usługi GitHub.

1. Kliknij przycisk **Dalej: kompiluj >** , aby edytować konfigurację kompilacji.

1. Ustaw _lokalizację aplikacji_ na **/_site**.

1. Pozostaw pustą _lokalizację artefaktu aplikacji_ .

   Wartość _lokalizacji interfejsu API_ nie jest konieczna, ponieważ w tej chwili nie jest WDRAŻANY interfejs API.

### <a name="review-and-create"></a>Przegląd i tworzenie

1. Kliknij przycisk **Recenzja + Utwórz** , aby sprawdzić, czy szczegóły są poprawne.

1. Kliknij przycisk **Utwórz** , aby rozpocząć tworzenie Web Apps statycznej platformy Azure i zainicjować akcję GitHub na potrzeby wdrożenia.

1. Wdrożenie będzie najpierw kończyć się niepowodzeniem, ponieważ plik przepływu pracy wymaga pewnych ustawień specyficznych dla Jekyll. Aby dodać te ustawienia, przejdź do terminalu i Pobierz zatwierdzenie z akcją GitHub na swoją maszynę.

   ```bash
   git pull
   ```

1. Otwórz aplikację Jekyll w edytorze tekstów i Otwórz plik _. GitHub/przepływy pracy/Azure-Pages-<WORKFLOW_NAME>. yml_ .

1. Przed wierszem `- name: Build And Deploy` Dodaj następujący blok konfiguracji.

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1.59.1
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. Zatwierdź zaktualizowany przepływ pracy i wypchnij do usługi GitHub.

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. Poczekaj na zakończenie akcji usługi GitHub.

1. W oknie _przegląd_ Azure Portal kliknij link _adresu URL_ , aby otworzyć wdrożoną aplikację.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Wdrożona aplikacja":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](custom-domain.md)
