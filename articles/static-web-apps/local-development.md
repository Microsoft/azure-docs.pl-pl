---
title: Skonfiguruj lokalne Programowanie dla Web Apps statycznej platformy Azure
description: Dowiedz się, jak skonfigurować lokalne środowisko deweloperskie dla Web Apps statycznej platformy Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 4d6dae8a4f4ed83af3103e95e711bacdb62cf522
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326171"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Skonfiguruj lokalne Programowanie dla usługi Azure static Web Apps Preview

Wystąpienie Web Apps static platformy Azure składa się z dwóch różnych typów aplikacji. Pierwsza to aplikacja internetowa dla zawartości statycznej. Aplikacje sieci Web są często tworzone za pomocą platform i bibliotek frontonu albo za pomocą statycznych generatorów witryn. Drugim aspektem jest interfejs API, który jest aplikacją Azure Functions, która zapewnia bogate środowisko programistyczne zaplecza.

W przypadku korzystania z chmury usługa Azure static Web Apps bezproblemowo mapuje żądania do `api` trasy z aplikacji sieci Web do aplikacji Azure Functions bez konieczności konfigurowania mechanizmu CORS. Lokalnie należy skonfigurować aplikację do naśladowania tego zachowania.

W tym artykule przedstawiono zalecane najlepsze rozwiązania dotyczące programowania lokalnego, w tym następujące pojęcia:

- Konfigurowanie aplikacji sieci Web pod kątem zawartości statycznej
- Konfigurowanie aplikacji Azure Functions dla interfejsu API aplikacji
- Debugowanie i uruchamianie aplikacji
- Najlepsze rozwiązania dotyczące struktury plików i folderów aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla Visual Studio Code
- [Rozszerzenie serwera na żywo](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) dla Visual Studio Code
  - Niezbędny tylko w przypadku, gdy nie jest używana platforma języka JavaScript frontonu lub interfejs wiersza polecenia statycznego generatora witryn

## <a name="run-projects-locally"></a>Uruchamianie projektów lokalnie

Uruchamianie statycznej aplikacji sieci Web platformy Azure lokalnie obejmuje trzy procesy, w zależności od tego, czy projekt zawiera interfejs API.

- Uruchamianie lokalnego serwera sieci Web
- Uruchamianie interfejsu API
- Łączenie projektu sieci Web z interfejsem API

W zależności od tego, jak witryna sieci Web jest skompilowana, lokalny serwer internetowy może lub nie musi być wymagany do uruchamiania aplikacji w przeglądarce. W przypadku korzystania z platform w języku JavaScript frontonu i statycznych generatorów witryn ta funkcja jest wbudowana w odpowiednie interfejsów wiersza polecenia (interfejsy wiersza polecenia). Poniższe łącza wskazują na odwołanie do interfejsu wiersza polecenia dla wybranych platform, bibliotek i generatorów.

### <a name="javascript-frameworks-and-libraries"></a>Struktury i biblioteki języka JavaScript

- [Interfejs wiersza polecenia platformy Angular](https://angular.io/cli)
- [Interfejs wiersza polecenia Vue](https://cli.vuejs.org/guide/creating-a-project.html)
- [Interfejs wiersza polecenia reagowania](https://create-react-app.dev/)

### <a name="static-site-generators"></a>Statyczne generatory lokacji

- [Interfejs wiersza polecenia Gatsby](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

Jeśli używasz narzędzia interfejsu wiersza polecenia, aby obsłużyć swoją witrynę, możesz przejść do sekcji [Uruchamianie interfejsu API](#run-api-locally) .

### <a name="running-a-local-web-server-with-live-server"></a>Uruchamianie lokalnego serwera sieci Web z serwerem na żywo

Rozszerzenie serwer na żywo dla Visual Studio Code zapewnia lokalny serwer sieci Web, który obsługuje zawartość statyczną.

#### <a name="create-a-repository"></a>Tworzenie repozytorium

1. Upewnij się, że zalogowano się do usługi GitHub, a następnie przejdź do [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) i Utwórz nowy projekt GitHub o nazwie **Wanili-API**przy użyciu tego szablonu.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="Okno nowego repozytorium GitHub":::

1. Otwórz program Visual Studio Code.

1. Naciśnij klawisz **F1**, aby otworzyć paletę poleceń.

1. W polu wyszukiwania wpisz **klon** , a następnie wybierz pozycję **git: Klonuj**.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="Okno nowego repozytorium GitHub":::

1. Wprowadź następującą wartość dla **adresu URL repozytorium**.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. Wybierz lokalizację folderu dla nowego projektu.

1. Po wyświetleniu monitu, aby otworzyć Sklonowane repozytorium, wybierz pozycję **Otwórz**.

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="Okno nowego repozytorium GitHub":::

Visual Studio Code otwiera sklonowany projekt w edytorze.

### <a name="run-the-website-locally-with-live-server"></a>Uruchamianie witryny sieci Web lokalnie z serwerem na żywo

1. Naciśnij klawisz **F1**, aby otworzyć paletę poleceń.

1. W polu wyszukiwania wpisz tekst **Live** Server, a następnie wybierz pozycję **Live Server: Open with Live Server**

    Zostanie otwarta karta przeglądarka, w której zostanie wyświetlona aplikacja.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="Okno nowego repozytorium GitHub":::

    Ta aplikacja wysyła żądanie HTTP do `api/message` punktu końcowego. Teraz to żądanie kończy się niepowodzeniem, ponieważ część interfejsu API tej aplikacji musi zostać uruchomiona.

### <a name="run-api-locally"></a>Uruchom interfejs API lokalnie

Interfejsy API usługi Azure static Web Apps są obsługiwane przez Azure Functions. Aby uzyskać szczegółowe informacje dotyczące dodawania interfejsu API do statycznego projektu Web Apps platformy Azure, zobacz [Dodawanie interfejsu API do statycznej Web Apps platformy Azure z Azure Functions](add-api.md) .

W ramach procesu tworzenia interfejsu API konfiguracja uruchamiania jest tworzona dla Visual Studio Code. Ta konfiguracja znajduje się w folderze _. programu vscode_ . Ten folder zawiera wszystkie wymagane ustawienia dotyczące kompilowania i uruchamiania interfejsu API lokalnie.

1. W Visual Studio Code naciśnij klawisz **F5** , aby uruchomić interfejs API.

1. Zostanie otwarte nowe wystąpienie terminalu pokazujące dane wyjściowe procesu kompilacji interfejsu API.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="Okno nowego repozytorium GitHub":::

   Pasek stanu w Visual Studio Code jest teraz pomarańczowy. Ten kolor wskazuje, że interfejs API jest teraz uruchomiony i debuger jest dołączony.

1. Następnie naciśnij **klawisze Ctrl/Cmd** i kliknij adres URL w terminalu, aby otworzyć okno przeglądarki, które wywołuje interfejs API.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="Okno nowego repozytorium GitHub":::

### <a name="debugging-the-api"></a>Debugowanie interfejsu API

1. Otwórz plik _API/GetMessage/index.js_ w Visual Studio Code.

1. Kliknij lewy margines w wierszu 2, aby ustawić punkt przerwania. Zostanie wyświetlona czerwona kropka, która wskazuje, że punkt przerwania jest ustawiony.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Okno nowego repozytorium GitHub":::

1. W przeglądarce Odśwież stronę z systemem <http://127.0.0.1:7071/api/message> .

1. Punkt przerwania został trafiony w Visual Studio Code i wykonywanie programu jest wstrzymane.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Okno nowego repozytorium GitHub":::

   Pełne [środowisko debugowania jest dostępne w Visual Studio Code](https://code.visualstudio.com/Docs/editor/debugging) dla interfejsu API.

1. Naciśnij przycisk **Kontynuuj** na pasku debugowania, aby kontynuować wykonywanie.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Okno nowego repozytorium GitHub":::

### <a name="calling-the-api-from-the-application"></a>Wywoływanie interfejsu API z aplikacji

Po wdrożeniu usługa Azure static Web Apps automatycznie mapuje te żądania do punktów końcowych w folderze _interfejsu API_ . To mapowanie zapewnia, że żądania z aplikacji do interfejsu API wyglądają jak w poniższym przykładzie.

```javascript
let response = await fetch("/api/message");
```

W zależności od tego, czy aplikacja została skompilowana za pomocą interfejsu wiersza polecenia języka JavaScript, istnieją dwa sposoby konfigurowania ścieżki do `api` trasy podczas lokalnego uruchamiania aplikacji.

- Pliki konfiguracji środowiska (zalecane dla środowisk i bibliotek JavaScript)
- Lokalny serwer proxy

### <a name="environment-configuration-files"></a>Pliki konfiguracji środowiska

Jeśli tworzysz aplikację z platformami frontonu z interfejsem wiersza polecenia, należy używać plików konfiguracji środowiska. Każda struktura lub biblioteka ma inny sposób obsługi tych plików konfiguracji środowiska. Często istnieje plik konfiguracji do programowania, który jest używany, gdy aplikacja działa lokalnie, a drugi dla środowiska produkcyjnego, który jest używany, gdy aplikacja działa w środowisku produkcyjnym. Interfejs wiersza polecenia dla środowiska JavaScript lub statycznego generatora witryn, którego używasz, automatycznie będzie znać użycie pliku deweloperskiego lokalnie i pliku produkcyjnego, gdy aplikacja jest skompilowana przez Web Apps statycznej platformy Azure.

W pliku konfiguracji programowanie można określić ścieżkę do interfejsu API, który wskazuje na lokalizację lokalną, w `http:127.0.0.1:7071` której interfejs API lokacji działa lokalnie.

```
API=http:127.0.0.1:7071/api
```

W pliku konfiguracji produkcyjnej określ ścieżkę do interfejsu API jako `api` . Dzięki temu aplikacja będzie wywoływała interfejs API za pośrednictwem "yoursite.com/api" podczas działania w środowisku produkcyjnym.

```
API=api
```

Te wartości konfiguracyjne mogą być przywoływane jako zmienne środowiskowe węzła w języku JavaScript aplikacji sieci Web.

```js
let response = await fetch(`${process.env.API}/message`);
```

Jeśli interfejs wiersza polecenia jest używany do uruchamiania lokacji w trybie programistycznym lub do kompilowania lokacji dla środowiska produkcyjnego, `process.env.API` wartość zostanie zastąpiona wartością z odpowiedniego pliku konfiguracji.

Aby uzyskać więcej informacji na temat konfigurowania plików środowiskowych dla platform i bibliotek języka JavaScript frontonu, zobacz następujące artykuły:

- [Zmienne środowiskowe kątowe](https://angular.io/guide/build#configuring-application-environments)
- [Reagowanie — Dodawanie niestandardowych zmiennych środowiskowych](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [VUE — tryby i zmienne środowiskowe](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Uruchom ponownie serwer Live

1. Naciśnij klawisz **F1** , aby otworzyć paletę poleceń w Visual Studio Code.

1. Wpisz **serwer Live Server** i wybierz pozycję **serwer aktywny: Zatrzymaj serwer na żywo**.

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Okno nowego repozytorium GitHub":::

1. Naciśnij klawisz **F1**, aby otworzyć paletę poleceń.

1. Wpisz **Live Server** i wybierz pozycję **Live Server: Open with Live Server**.

1. Odśwież aplikację uruchomioną w systemie `http://locahost:3000` . W przeglądarce jest teraz wyświetlany komunikat zwrócony z interfejsu API.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="Okno nowego repozytorium GitHub":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie ustawień aplikacji](application-settings.md)
