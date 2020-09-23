---
title: Przykład Hero rozmowy grupowej
titleSuffix: An Azure Communication Services sample overview
description: Omówienie przykładu Hero rozmowy przy użyciu usług Azure Communication Services, aby umożliwić deweloperom dowiedzieć się więcej o wewnętrznych działaniach przykładowych i Dowiedz się, jak go zmodyfikować.
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 295c4bde64ad21a19d21fd48f2556114b26b202d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947394"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>Wprowadzenie do przykładu Hero rozmowy z grupą

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

**Przykład Hero rozmowy z grupą** usług Azure Communication Services demonstruje, jak Biblioteka klienta sieci Web czatu usług komunikacyjnych może służyć do tworzenia środowiska wywołującego grupę.

W tym przykładowym samouczku szybki start dowiesz się, jak działa przykład przed uruchomieniem przykładu na komputerze lokalnym. Następnie zainstalujemy przykład na platformie Azure przy użyciu własnych zasobów usług Azure Communications Services.

> [!IMPORTANT]
> [Pobierz przykład z witryny GitHub](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>Omówienie

Przykład zawiera aplikację po stronie klienta i aplikację po stronie serwera. **Aplikacja po stronie klienta** to aplikacja sieci Web do reagowania/Redux, która korzysta z platformy interfejsu użytkownika Fluent firmy Microsoft. Ta aplikacja wysyła żądania do ASP.NET Core **aplikacji po stronie serwera** , która pomaga aplikacji po stronie klienta połączyć się z platformą Azure. 

Oto jak wygląda przykład:

:::image type="content" source="./media/chat/landing-page.png" alt-text="Zrzut ekranu przedstawiający stronę docelową przykładowej aplikacji.":::

Po naciśnięciu przycisku "Rozpocznij rozmowę" aplikacja sieci Web pobierze token dostępu użytkownika z aplikacji po stronie serwera. Token ten jest następnie używany do łączenia aplikacji klienta z usługami Azure Communications Services. Po pobraniu tokenu zostanie wyświetlony monit o podanie nazwy i znaku emoji, który będzie reprezentował Ciebie w rozmowie. 

:::image type="content" source="./media/chat/pre-chat.png" alt-text="Zrzut ekranu przedstawiający ekran przedczatu aplikacji.":::

Po skonfigurowaniu nazwy wyświetlanej i Emoji można dołączyć do sesji rozmowy. Zostanie wyświetlona główna Kanwa rozmowy, w której znajduje się podstawowe doświadczenie z rozmowy.

:::image type="content" source="./media/chat/main-app.png" alt-text="Zrzut ekranu przedstawiający ekran główny aplikacji przykładowej.":::

Składniki głównego ekranu czatu:

- **Główny obszar rozmowy**: jest to podstawowe środowisko rozmowy, w którym użytkownicy mogą wysyłać i odbierać wiadomości. Aby wysyłać komunikaty, możesz użyć obszaru wprowadzania i nacisnąć klawisz ENTER (lub użyć przycisku Wyślij). Odebrane komunikaty czatu są kategoryzowane przez nadawcę przy użyciu poprawnej nazwy i Emoji. W obszarze rozmowa są wyświetlane dwa typy powiadomień: 1) wpisywanie powiadomień podczas wpisywania przez użytkownika i 2) wysyłania i odczytywania powiadomień dotyczących komunikatów.
- **Nagłówek**: to miejsce, w którym użytkownik zobaczy tytuł wątku rozmowy i kontrolki do przełączania uczestników i ustawień paski boczne oraz przycisk opuszczania, aby zakończyć sesję rozmowy.
- **Pasek boczny**: wskazuje, że uczestnicy i informacje o ustawieniach są wyświetlane po przełączeniu przy użyciu kontrolek w nagłówku. Pasek boczny uczestników zawiera listę uczestników rozmowy i link umożliwiający Zapraszanie uczestników do sesji rozmowy. Pasek boczny ustawienia umożliwia skonfigurowanie tytułu wątku rozmowy. 

Poniżej znajdziesz więcej informacji na temat wymagań wstępnych i kroków związanych z konfigurowaniem przykładu.

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (8.11.2 i nowsze)](https://nodejs.org/en/download/)
- [Visual Studio (2017 i nowsze)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2,2](https://dotnet.microsoft.com/download/dotnet-core/2.2) (Pamiętaj, aby zainstalować wersję odpowiadającą wystąpieniu programu Visual studio, 32 vs 64 bit)
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../quickstarts/create-communication-resource.md). Musisz zarejestrować **Parametry połączenia** zasobu dla tego przewodnika Szybki Start.

## <a name="locally-deploying-the-service--client-app"></a>Lokalne wdrażanie usługi & aplikacji klienckiej

Przykład rozmowy jednowątkowej to zasadniczo dwie "aplikacje" aplikacji klienta i serwera.

Otwórz program Visual Studio w programie Chat. csproj i uruchom w trybie debugowania, co spowoduje uruchomienie usługi frontonu programu Chat. Gdy aplikacja serwera zostanie odwiedzana z przeglądarki, przekieruje ruch do usługi frontonu wdrożonej lokalnie.

Możesz przetestować przykład lokalnie, otwierając wiele sesji przeglądarki z adresem URL rozmowy w celu symulowania rozmowy z wieloma użytkownikami.

### <a name="before-running-the-sample-for-the-first-time"></a>Przed uruchomieniem przykładu po raz pierwszy

1. Otwórz wystąpienie programu PowerShell, terminalu systemu Windows, wiersza polecenia lub równoważne i przejdź do katalogu, do którego chcesz sklonować przykład.
2. `git clone`
3. Przejdź do folderu **Chat/ClientApp** i uruchom `npm run setup`
   1. Jeśli zobaczysz błąd 1, poszukaj powyżej w danych wyjściowych dla adresu URL, na którym musisz przejść do autoryzacji klienta. (Adres URL będzie wyglądać następująco: `app.vssps.visualstudio.com/oauth2/authorize?clientid=...` ) Po odwiedzeniu adresu URL w przeglądarce Skopiuj polecenie z okna przeglądarki i uruchom je.
   2. Uruchom polecenie `npm run setup` ponownie po wykonaniu poprzedniego kroku.
4. Pobierz `Connection String` z Azure Portal. Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Tworzenie zasobów usługi Azure Communication](../quickstarts/create-communication-resource.md)
5. Po otrzymaniu programu `Connection String` Dodaj parametry połączenia do okna **rozmowa/appsettings.jsw** pliku znalezionym w folderze rozmowa. Wprowadź parametry połączenia w zmiennej: `ResourceConnectionString` .

### <a name="local-run"></a>Uruchamianie lokalne

1. Przejdź do folderu czatu
2. Otwórz `Chat.csproj` rozwiązanie w programie Visual Studio
3. Uruchom `Chat` projekt. *

* Przeglądarka zostanie otwarta przy użyciu nazwy localhost: 5000 (gdzie węzeł wdraża aplikację kliencką). Aplikacja nie jest obsługiwana w programie Internet Explorer.

#### <a name="troubleshooting"></a>Rozwiązywanie problemów

- Rozwiązanie nie kompiluje się, zgłasza błędy podczas instalacji/kompilacji NPM

Oczyść/Kompiluj rozwiązanie C#

## <a name="publish-the-sample-to-azure"></a>Publikuj przykład na platformie Azure

1. Kliknij prawym przyciskiem myszy `Chat` projekt i wybierz polecenie Publikuj.
2. Utwórz nowy profil publikowania i wybierz subskrypcję platformy Azure.
3. Przed opublikowaniem Dodaj parametry połączenia z `Edit App Service Settings` i wypełnij `ResourceConnectionString` jako klucz i podaj parametry połączenia (skopiowane z appsettings.json) jako wartość.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Informacje o [pojęciach związanych z rozmowami](../concepts/chat/concepts.md)
- Zapoznaj się z naszą [biblioteką klienta czatu](../concepts/chat/sdk-features.md)

## <a name="additional-reading"></a>Materiały uzupełniające

- [Azure Communication Preview](https://github.com/Azure/communication-preview) — aby dowiedzieć się więcej o zestawie SDK sieci Web programu Chat
- [Redux](https://redux.js.org/) — zarządzanie stanem po stronie klienta
- [FluentUI](https://developer.microsoft.com/fluentui#/) — Biblioteka interfejsu użytkownika zasilanego przez firmę Microsoft
- [Reagowanie](https://reactjs.org/) — Biblioteka do tworzenia interfejsów użytkownika
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) — platforma do tworzenia aplikacji sieci Web
