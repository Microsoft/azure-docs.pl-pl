---
title: Przykładowa Grupa wywołująca Hero
titleSuffix: An Azure Communication Services sample overview
description: Omówienie wywoływania przykładu Hero przy użyciu usług Azure Communication Services, aby umożliwić deweloperom dowiedzieć się więcej o wewnętrznych działaniach przykładu.
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: caee5686695594604f49dcbade54342a9134abc0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947485"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Wprowadzenie do grupy wywołującej przykład Hero

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

**Przykład Hero grupy** usług komunikacyjnych platformy Azure demonstruje, jak usługi komunikacyjne wywołujące bibliotekę klienta sieci Web mogą być używane do tworzenia środowiska wywołującego grupę.

W tym przykładowym samouczku szybki start dowiesz się, jak działa przykład przed uruchomieniem przykładu na komputerze lokalnym. Następnie zainstalujemy przykład na platformie Azure przy użyciu własnych zasobów usług Azure Communications Services.

> [!IMPORTANT]
> [Pobierz przykład z witryny GitHub](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>Omówienie

Przykład zawiera aplikację po stronie klienta i aplikację po stronie serwera. **Aplikacja po stronie klienta** to aplikacja sieci Web do reagowania/Redux, która korzysta z platformy interfejsu użytkownika Fluent firmy Microsoft. Ta aplikacja wysyła żądania do ASP.NET Core **aplikacji po stronie serwera** , która pomaga aplikacji po stronie klienta połączyć się z platformą Azure. 

Oto jak wygląda przykład:

:::image type="content" source="./media/calling/landing-page.png" alt-text="Zrzut ekranu przedstawiający stronę docelową przykładowej aplikacji.":::

Po naciśnięciu przycisku "Rozpocznij połączenie" aplikacja sieci Web pobierze token dostępu użytkownika z aplikacji po stronie serwera. Token ten jest następnie używany do łączenia aplikacji klienta z usługami Azure Communications Services. Po pobraniu tokenu zostanie wyświetlony monit o określenie aparatu i mikrofonu, który ma być używany. Można wyłączyć lub włączyć urządzenia z kontrolkami przełączania:

:::image type="content" source="./media/calling/pre-call.png" alt-text="Zrzut ekranu przedstawiający ekran przed wywołaniem przykładowej aplikacji.":::

Po skonfigurowaniu wyświetlanej nazwy i urządzeń można przyłączyć się do sesji wywołania. Teraz zobaczysz główną kanwę wywołań, w której działa podstawowe środowisko wywoływania.

:::image type="content" source="./media/calling/main-app.png" alt-text="Zrzut ekranu przedstawiający ekran główny przykładowej aplikacji.":::

Składniki głównego ekranu wywołującego:

- **Galeria multimediów**: Główny etap pokazujący uczestników. Jeśli Uczestnik ma włączony aparat fotograficzny, w tym miejscu są wyświetlane ich kanały wideo. Każdy uczestnik ma pojedynczy kafelek, który pokazuje ich nazwę wyświetlaną i strumień wideo (gdy istnieje)
- **Nagłówek**: to miejsce, w którym znajdują się podstawowe kontrolki wywołania, aby przełączać ustawienia i pasek boczny uczestnika, włączać lub wyłączać i mieszać zawartość ekranu oraz pozostawiać wywołanie.
- **Pasek boczny**: to miejsce, w którym informacje o uczestnikach i ustawieniach są wyświetlane po przełączeniu przy użyciu kontrolek w nagłówku. Składnik może zostać odrzucony przy użyciu "X" w prawym górnym rogu. Na pasku bocznym uczestników zostanie wyświetlona lista uczestników i link pozwalający zaprosić więcej użytkowników do rozmowy. Pasek boczny ustawienia umożliwia skonfigurowanie ustawień mikrofonu i aparatu.

Poniżej znajdziesz więcej informacji na temat wymagań wstępnych, kroki konfigurowania przykładu oraz Samouczki krok po kroku, które ułatwiają zapoznanie się z różnymi składnikami.

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 i nowsze)](https://nodejs.org/en/download/)
- [Visual Studio (2019 i nowsze)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2,2](https://dotnet.microsoft.com/download/dotnet-core/2.2) (Pamiętaj, aby zainstalować wersję odpowiadającą wystąpieniu programu Visual studio, 32 vs 64 bit)
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../quickstarts/create-communication-resource.md). Musisz zarejestrować **Parametry połączenia** zasobu dla tego przewodnika Szybki Start.

## <a name="locally-deploy-the-service--client-applications"></a>Lokalne wdrażanie usługi & aplikacji klienckich

Przykładowa Grupa wywołująca to zasadniczo dwie aplikacje: ClientApp i Service.NET.

Jeśli chcemy wdrożyć lokalnie, musimy uruchomić obie aplikacje. Gdy aplikacja serwera zostanie odwiedzana z przeglądarki, użyje lokalnie wdrożonego ClientApp do obsługi środowiska użytkownika.

Możesz przetestować przykład lokalnie, otwierając wiele sesji przeglądarki z adresem URL wywołania w celu symulowania wywołania z wieloma użytkownikami.

### <a name="before-running-the-sample-for-the-first-time"></a>Przed uruchomieniem przykładu po raz pierwszy

1. Otwórz wystąpienie programu PowerShell, terminalu systemu Windows, wiersza polecenia lub równoważne i przejdź do katalogu, do którego chcesz sklonować przykład.
2. `git clone`
3. Przejdź do **folderu calling/ClientApp** i uruchom `npm run setup`
   1. Jeśli zobaczysz błąd 1, poszukaj powyżej w danych wyjściowych dla adresu URL, na którym musisz przejść do autoryzacji klienta. (Adres URL będzie wyglądać następująco: `app.vssps.visualstudio.com/oauth2/authorize?clientid=...` ) Po odwiedzeniu adresu URL w przeglądarce Skopiuj polecenie z okna przeglądarki i uruchom je.
   2. Uruchom polecenie `npm run setup-vsts-auth` ponownie po wykonaniu poprzedniego kroku.
4. Pobierz `Connection String` z Azure Portal. Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Tworzenie zasobów usługi Azure Communication](../quickstarts/create-communication-resource.md)
5. Po otrzymaniu parametrów połączenia Dodaj parametry połączenia do **wywołania/appsetting.jsw** pliku znalezionym w folderze usługi .NET. Wprowadź parametry połączenia w zmiennej: `ResourceConnectionString` .

### <a name="local-run"></a>Uruchomienie lokalne

1. Przejdź do folderu wywołującego
2. Otwórz `Calling.csproj` rozwiązanie w programie Visual Studio
2. Uruchom `Calling` projekt *

* Przeglądarka zostanie otwarta w lokalizacji `localhost:5000` (gdzie węzeł wdraża aplikację kliencką). Aplikacja nie jest obsługiwana w programie Internet Explorer.

#### <a name="troubleshooting"></a>Rozwiązywanie problemów

- Rozwiązanie nie kompiluje się; zgłasza błędy podczas instalacji/kompilacji NPM.

   Spróbuj wyczyścić/skompilować projekty.

## <a name="publish-the-sample-to-azure"></a>Publikuj przykład na platformie Azure

1. Kliknij prawym przyciskiem myszy `Calling` projekt i wybierz polecenie Publikuj.
2. Utwórz nowy profil publikowania i wybierz subskrypcję platformy Azure.
3. Przed opublikowaniem Dodaj parametry połączenia z `Edit App Service Settings` i wypełnij `ResourceConnectionString` jako klucz i podaj parametry połączenia (skopiowane z appsettings.json) jako wartość.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z [pomocą biblioteki klienta wywołującego](../quickstarts/voice-video-calling/calling-client-samples.md)
- Informacje o [wywoływaniu możliwości biblioteki klienta](../quickstarts/voice-video-calling/calling-client-samples.md)
- Dowiedz się więcej o [sposobie wywoływania programu](../concepts/voice-video-calling/about-call-types.md)

## <a name="additional-reading"></a>Materiały uzupełniające

- [Azure Communication Preview](https://github.com/Azure/communication-preview) — aby dowiedzieć się więcej o wywołującym zestawie SDK sieci Web
- [Redux](https://redux.js.org/) — zarządzanie stanem po stronie klienta
- [FluentUI](https://developer.microsoft.com/fluentui#/) — Biblioteka interfejsu użytkownika zasilanego przez firmę Microsoft
- [Reagowanie](https://reactjs.org/) — Biblioteka do tworzenia interfejsów użytkownika
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) — platforma do tworzenia aplikacji sieci Web
