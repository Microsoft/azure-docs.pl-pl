---
title: 'Samouczek: wdrażanie Next.jsych witryn sieci Web renderowanych statycznie w usłudze Azure static Web Apps'
description: Generuj i wdrażaj Next.js Lokacje dynamiczne przy użyciu statycznego Web Apps platformy Azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: a22d06137c3ec17851280605ac85c94ef8b342cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97563079"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps-preview"></a>Wdróż statyczne, renderowane Next.js witryny sieci Web w usłudze Azure static Web Apps Preview

W ramach tego samouczka nauczysz się wdrożyć [Next.js](https://nextjs.org) wygenerowaną statyczną witrynę sieci web do [Web Apps statycznej platformy Azure](overview.md). Aby rozpocząć, dowiesz się, jak skonfigurować, skonfigurować i wdrożyć aplikację Next.js. W trakcie tego procesu warto również zapoznać się z typowymi wyzwaniami często spotykanymi podczas generowania stron statycznych przy użyciu Next.js

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).
- Konto usługi GitHub. [Utwórz konto bezpłatnie](https://github.com/join).
- [Node.js](https://nodejs.org) zainstalowane.

## <a name="set-up-a-nextjs-app"></a>Konfigurowanie aplikacji Next.js

Zamiast korzystać z interfejsu wiersza polecenia Next.js do tworzenia aplikacji, możesz użyć repozytorium początkowego, które zawiera istniejącą aplikację Next.js. To repozytorium zawiera aplikację Next.jsą z trasami dynamicznymi, która wyróżnia typowy problem związany z wdrażaniem. Trasy dynamiczne wymagają dodatkowej konfiguracji wdrożenia, która w tym momencie będzie dowiedzieć się więcej.

Aby rozpocząć, Utwórz nowe repozytorium na koncie usługi GitHub z repozytorium szablonów.

1. Przejdź do [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate)
1. Nadaj nazwę repozytorium **nextjs-Starter**
1. Następnie Sklonuj nowe repozytorium na komputerze. Pamiętaj, aby zamienić na `<YOUR_GITHUB_ACCOUNT_NAME>` nazwę swojego konta.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Przejdź do nowo sklonowanej aplikacji Next.js:

   ```bash
   cd nextjs-starter
   ```

1. Zainstaluj zależności:

    ```bash
    npm install
    ```

1. Rozpocznij Next.js aplikacji w programie Development:

    ```bash
    npm run dev
    ```

Przejdź do strony `http://localhost:3000` , aby otworzyć aplikację, w której powinna zostać otwarta Następująca witryna sieci Web w preferowanej przeglądarce:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Uruchom aplikację Next.js":::

Po kliknięciu architektury/biblioteki powinna zostać wyświetlona strona szczegółów wybranego elementu:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Strona szczegółów":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Generuj statyczną witrynę sieci Web na podstawie kompilacji Next.js

Gdy tworzysz witrynę Next.js przy użyciu programu `npm run build` , aplikacja jest skompilowana jako tradycyjna aplikacja sieci Web, a nie lokacja statyczna. Aby wygenerować lokację statyczną, należy użyć następującej konfiguracji aplikacji.

1. Aby skonfigurować trasy statyczne, Utwórz plik o nazwie _next.config.js_ w katalogu głównym projektu i Dodaj następujący kod.

    ```javascript
    module.exports = {
      trailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```
    
      Ta konfiguracja `/` jest mapowana na stronę Next.js, która jest obsługiwana dla `/` trasy, a to jest plik stronicowania _stron/index.js_ .

1. Zaktualizuj _package.jsna_ skrypcie kompilacji, aby generować również lokację statyczną po skompilowaniu przy użyciu `next export` polecenia. `export`Polecenie generuje lokację statyczną.

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    Teraz, gdy to polecenie jest stosowane, Web Apps statyczne uruchomi `build` skrypt przy każdym wypchnięciu zatwierdzenia.

1. Generuj lokację statyczną:

    ```bash
    npm run build
    ```

    Lokacja statyczna jest generowana i kopiowana do folderu _out_ w katalogu głównym katalogu roboczego.

    > [!NOTE]
    > Ten folder znajduje się w pliku _. gitignore_ , ponieważ powinien być generowany przez usługę ciągłej integracji/ciągłego wdrażania.

## <a name="push-your-static-website-to-github"></a>Wypychanie statycznej witryny internetowej do usługi GitHub

Usługa Azure static Web Apps wdraża aplikację z repozytorium GitHub i kontynuuje wykonywanie operacji dla każdej wypychanej zatwierdzeń w wysuniętej gałęzi. Użyj następujących poleceń, aby zsynchronizować zmiany w serwisie GitHub.

1. Przemieszczanie wszystkich zmienionych plików

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

### <a name="create-a-static-app"></a>Tworzenie aplikacji statycznej

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

   :::image type="content" source="media/deploy-nextjs/create-static-web-app.png" alt-text="Tworzenie statycznej aplikacji internetowej":::

### <a name="add-a-github-repository"></a>Dodawanie repozytorium GitHub

Nowe konto statyczne Web Apps musi mieć dostęp do repozytorium za pomocą aplikacji Next.js, dzięki czemu może automatycznie wdrażać zatwierdzenia.

1. Kliknij **przycisk Zaloguj się przy użyciu usługi GitHub**
1. Wybierz **organizację** , w ramach której utworzono repozytorium dla projektu Next.js. może to być nazwa użytkownika usługi GitHub.
1. Znajdź i wybierz nazwę utworzonego wcześniej repozytorium.
1. Wybierz pozycję **główna** jako gałąź z listy rozwijanej *rozgałęzienie* .

   :::image type="content" source="media/deploy-nextjs/connect-github.png" alt-text="Łączenie z usługą GitHub":::

### <a name="configure-the-build-process"></a>Skonfiguruj proces kompilacji

Usługa Azure static Web Apps jest wbudowana w taki sposób, aby automatycznie wykonywała typowe zadania, takie jak Instalowanie modułów npm i uruchamianie ich `npm run build` podczas każdego wdrożenia. Istnieje jednak kilka ustawień, takich jak folder źródłowy aplikacji i folder docelowy kompilacji, które należy skonfigurować ręcznie.

1. Kliknij kartę **kompilacja** , aby skonfigurować statyczny folder wyjściowy.

   :::image type="content" source="media/deploy-nextjs/build-tab.png" alt-text="Karta kompilacja":::

2. Wpisz **wartość w polu tekstowym** *Lokalizacja artefaktu aplikacji* .

### <a name="review-and-create"></a>Przegląd i tworzenie

1. Kliknij przycisk **Recenzja + Utwórz** , aby sprawdzić, czy szczegóły są poprawne.
1. Kliknij przycisk **Utwórz** , aby rozpocząć tworzenie zasobu, a także zainicjować akcję usługi GitHub na potrzeby wdrożenia.
1. Po zakończeniu wdrażania kliknij pozycję **Przejdź do zasobu** .
1. W oknie _Przegląd_ kliknij link *adresu URL* , aby otworzyć wdrożoną aplikację. 

Jeśli witryna internetowa natychmiast załaduje dane, wówczas przepływ pracy akcji GitHub w tle nadal działa. Po zakończeniu przepływu pracy można kliknąć pozycję Odśwież przeglądarkę, aby wyświetlić aplikację sieci Web.
Jeśli witryna internetowa natychmiast załaduje dane, wówczas przepływ pracy akcji GitHub w tle nadal działa. Po zakończeniu przepływu pracy można kliknąć pozycję Odśwież przeglądarkę, aby wyświetlić aplikację sieci Web.

Stan przepływów pracy akcji można sprawdzić, przechodząc do akcji dla repozytorium:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>Synchronizuj zmiany

Po utworzeniu aplikacji usługa Azure static Web Apps utworzyła plik przepływu pracy akcji GitHub w repozytorium. Musisz przenieść ten plik do repozytorium lokalnego, aby była synchronizowana historia usługi git.

Wróć do terminalu i uruchom następujące polecenie `git pull origin main` .

## <a name="configure-dynamic-routes"></a>Konfiguruj trasy dynamiczne

Przejdź do nowo wdrożonej lokacji i kliknij jeden z logo struktury lub biblioteki. Zamiast pobierania strony szczegółów pojawia się strona błędu 404.

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="404 na trasach dynamicznych":::

Przyczyną tego błędu jest to, że Next.js wygenerowana tylko Strona główna oparta na konfiguracji aplikacji.

## <a name="generate-static-pages-from-dynamic-routes"></a>Generuj strony statyczne z tras dynamicznych

1. Zaktualizuj plik _next.config.js_ tak, aby Next.js używał listy wszystkich dostępnych danych do generowania stron statycznych dla każdej struktury/biblioteki:

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     trailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > `exportPathMap`Funkcja jest funkcją asynchroniczną, więc można utworzyć żądanie do interfejsu API w tej funkcji i użyć zwróconej listy do wygenerowania ścieżek.

2. Wypchnij nowe zmiany do repozytorium GitHub i poczekaj kilka minut, a akcje usługi GitHub ponownie kompilują lokację. Po zakończeniu kompilacji błąd 404 zniknie.

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="404 przy stałych trasach dynamicznych":::

> [!div class="nextstepaction"]
> [Skonfiguruj domenę niestandardową](custom-domain.md)
