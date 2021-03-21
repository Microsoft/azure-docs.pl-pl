---
title: 'Samouczek: wdrażanie Nuxt.js witryny sieci Web renderowane na serwerze w usłudze Azure static Web Apps'
description: Generuj i wdrażaj Nuxt.js Lokacje dynamiczne przy użyciu statycznego Web Apps platformy Azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 8f21f5fa8ee9035fe594cecff37a63b1ef2115cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563470"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>Wdróż Nuxt.js witryny sieci Web renderowane na serwerze w usłudze Azure static Web Apps Preview

W ramach tego samouczka nauczysz się wdrożyć [Nuxt.js](https://nuxtjs.org) wygenerowaną statyczną witrynę sieci web do [Web Apps statycznej platformy Azure](overview.md). Aby rozpocząć, dowiesz się, jak skonfigurować, skonfigurować i wdrożyć aplikację Nuxt.js. W trakcie tego procesu warto również zapoznać się z typowymi wyzwaniami często spotykanymi podczas generowania stron statycznych przy użyciu Nuxt.js

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).
- Konto usługi GitHub. [Utwórz konto bezpłatnie](https://github.com/join).
- [Node.js](https://nodejs.org) zainstalowane.

## <a name="set-up-a-nuxtjs-app"></a>Konfigurowanie aplikacji Nuxt.js

Nowy projekt Nuxt.js można skonfigurować przy użyciu programu `create-nuxt-app` . Zamiast nowego projektu, w tym samouczku zaczynasz od klonowania istniejącego repozytorium. To repozytorium jest skonfigurowane w celu zademonstrowania sposobu wdrażania aplikacji dynamicznej Nuxt.js jako lokacji statycznej.

1. Utwórz nowe repozytorium na koncie usługi GitHub z repozytorium szablonów.
1. Przejdź do [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate)
1. Nadaj nazwę repozytorium **nuxtjs-Starter**
1. Następnie Sklonuj nowe repozytorium na komputerze. Pamiętaj, aby zastąpić <YOUR_GITHUB_ACCOUNT_NAME> nazwą swojego konta.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Przejdź do nowo sklonowanej aplikacji Nuxt.js:

   ```bash
   cd nuxtjs-starter
   ```

1. Zainstaluj zależności:

    ```bash
    npm install
    ```

1. Rozpocznij Nuxt.js aplikacji w programie Development:

    ```bash
    npm run dev
    ```

Przejdź do strony `http://localhost:3000` , aby otworzyć aplikację, w której powinna zostać otwarta Następująca witryna sieci Web w preferowanej przeglądarce:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Uruchom aplikację Nuxt.js":::

Po kliknięciu architektury/biblioteki powinna zostać wyświetlona strona szczegółów wybranego elementu:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Strona szczegółów":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Generuj statyczną witrynę sieci Web na podstawie kompilacji Nuxt.js

Gdy tworzysz witrynę Nuxt.js przy użyciu programu `npm run build` , aplikacja jest skompilowana jako tradycyjna aplikacja sieci Web, a nie lokacja statyczna. Aby wygenerować lokację statyczną, należy użyć następującej konfiguracji aplikacji.

1. Zaktualizuj skrypt kompilacji _package.jsw_ celu wygenerowania tylko lokacji statycznej przy użyciu `nuxt generate` polecenia:

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    Teraz, gdy to polecenie jest stosowane, Web Apps statyczne uruchomi `build` skrypt przy każdym wypchnięciu zatwierdzenia.

1. Generuj lokację statyczną:

    ```bash
    npm run build
    ```

    Nuxt.js wygeneruje lokację statyczną i skopiuje ją do folderu w języku _ROZKŁ_ w katalogu głównym katalogu roboczego.

    > [!NOTE]
    > Ten folder znajduje się w pliku _. gitignore_ , ponieważ powinien być generowany przez usługę ciągłej integracji/ciągłego wdrażania.

## <a name="push-your-static-website-to-github"></a>Wypychanie statycznej witryny internetowej do usługi GitHub

Usługa Azure static Web Apps wdraża aplikację z repozytorium GitHub i kontynuuje wykonywanie operacji dla każdej wypychanej zatwierdzeń w wysuniętej gałęzi. Użyj następujących poleceń, aby zsynchronizować zmiany w serwisie GitHub.

1. Przygotowanie wszystkich zmienionych plików:

    ```bash
    git add .
    ```

1. Zatwierdź wszystkie zmiany

    ```bash
    git commit -m "Update build config"
    ```

1. Wypchnij zmiany do usługi GitHub.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Wdróż statyczną witrynę sieci Web

Poniższe kroki pokazują, jak połączyć aplikację przekazana do usługi GitHub z usługą Azure static Web Apps. Na platformie Azure można wdrożyć aplikację w środowisku produkcyjnym.

### <a name="create-an-azure-static-web-apps-preview-resource"></a>Utwórz zasób usługi Azure static Web Apps w wersji zapoznawczej

1. Przejdź do [Azure Portal](https://portal.azure.com)
1. Kliknij pozycję **Utwórz zasób**
1. Wyszukaj usługę **Static Web Apps**
1. Kliknij pozycję **Static Web Apps (wersja zapoznawcza)**
1. Kliknij pozycję **Utwórz**

1. Wybierz subskrypcję z listy rozwijanej *subskrypcja* lub użyj wartości domyślnej.
1. Kliknij link **Nowy** poniżej listy rozwijanej *Grupa zasobów* . W polu *Nazwa nowej grupy zasobów* wpisz **mystaticsite** , a następnie kliknij przycisk **OK** .
1. Podaj globalnie unikatową nazwę aplikacji w polu tekstowym **Nazwa** . Prawidłowe znaki to `a-z` , `A-Z` , `0-9` , i `-` . Ta wartość jest używana jako prefiks adresu URL dla aplikacji statycznej w formacie `https://<APP_NAME>.azurestaticapps.net` .
1. Z listy rozwijanej *region* wybierz region znajdujący się najbliżej siebie.
1. Wybierz pozycję **bezpłatnie** z listy rozwijanej jednostka SKU.

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="Tworzenie statycznej aplikacji internetowej":::

### <a name="add-a-github-repository"></a>Dodawanie repozytorium GitHub

Nowe konto statyczne Web Apps musi mieć dostęp do repozytorium za pomocą aplikacji Nuxt.js, dzięki czemu może automatycznie wdrażać zatwierdzenia.

1. Kliknij **przycisk Zaloguj się przy użyciu usługi GitHub**
1. Wybierz **organizację** , w ramach której utworzono repozytorium dla projektu Nuxt.js. może to być nazwa użytkownika usługi GitHub.
1. Znajdź i wybierz nazwę utworzonego wcześniej repozytorium.
1. Wybierz pozycję **główna** jako gałąź z listy rozwijanej *rozgałęzienie* .

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="Łączenie z usługą GitHub":::

### <a name="configure-the-build-process"></a>Skonfiguruj proces kompilacji

Usługa Azure static Web Apps jest wbudowana w taki sposób, aby automatycznie wykonywała typowe zadania, takie jak Instalowanie modułów npm i uruchamianie ich `npm run build` podczas każdego wdrożenia. Istnieje jednak kilka ustawień, takich jak folder źródłowy aplikacji i folder docelowy kompilacji, które należy skonfigurować ręcznie.

1. Kliknij kartę **kompilacja** , aby skonfigurować statyczny folder wyjściowy.

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="Karta kompilacja":::

1. Wpisz **wartość w polu tekstowym** *Lokalizacja artefaktu aplikacji* .

### <a name="review-and-create"></a>Przegląd i tworzenie

1. Kliknij przycisk **Recenzja + Utwórz** , aby sprawdzić, czy szczegóły są poprawne.
1. Kliknij przycisk **Utwórz** , aby rozpocząć tworzenie zasobu, a także zainicjować akcję usługi GitHub na potrzeby wdrożenia.
1. Po zakończeniu wdrażania kliknij pozycję **Przejdź do zasobu** .
1. W oknie _Przegląd_ kliknij link *adresu URL* , aby otworzyć wdrożoną aplikację. 

Jeśli witryna internetowa natychmiast załaduje dane, wówczas przepływ pracy akcji GitHub w tle nadal działa. Po zakończeniu przepływu pracy można kliknąć pozycję Odśwież przeglądarkę, aby wyświetlić aplikację sieci Web.

Stan przepływów pracy akcji można sprawdzić, przechodząc do akcji dla repozytorium:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Synchronizuj zmiany

Po utworzeniu aplikacji usługa Azure static Web Apps utworzyła plik przepływu pracy akcji GitHub w repozytorium. Musisz przenieść ten plik do repozytorium lokalnego, aby była synchronizowana historia usługi git.

Wróć do terminalu i uruchom następujące polecenie `git pull origin main` .

## <a name="configure-dynamic-routes"></a>Konfiguruj trasy dynamiczne

Przejdź do nowo wdrożonej lokacji i kliknij jeden z logo struktury lub biblioteki. Zamiast pobierania strony szczegółów pojawia się strona błędu 404.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="404 na trasach dynamicznych":::

Przyczyną tego jest Nuxt.js wygenerowana lokacja statyczna. w tym celu będzie ona istniała tylko dla strony głównej. Nuxt.js może generować równoważne `.html` pliki statyczne dla każdego `.vue` pliku stron, ale wystąpił wyjątek. 

Jeśli strona jest stroną dynamiczną, na przykład `_id.vue` nie będzie miała wystarczającej ilości informacji do wygenerowania statycznego kodu HTML z takiej strony dynamicznej. Musisz jawnie podać możliwe ścieżki dla tras dynamicznych.

## <a name="generate-static-pages-from-dynamic-routes"></a>Generuj strony statyczne z tras dynamicznych

1. Zaktualizuj plik _nuxt.config.js_ tak, aby Nuxt.js używał listy wszystkich dostępnych danych do generowania stron statycznych dla każdej struktury/biblioteki:

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` jest funkcją asynchroniczną, więc można wykonać żądanie do interfejsu API w tej funkcji i użyć zwróconej listy do wygenerowania ścieżek.

2. Wypchnij nowe zmiany do repozytorium GitHub i poczekaj kilka minut, a akcje usługi GitHub ponownie kompilują lokację. Po zakończeniu kompilacji błąd 404 zniknie.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="404 przy stałych trasach dynamicznych":::

> [!div class="nextstepaction"]
> [Skonfiguruj domenę niestandardową](custom-domain.md)
