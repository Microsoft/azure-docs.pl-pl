---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d3586e03a3c6214390f4a8b72b1db5871d2d1900
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510790"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

**Przykład Hero grupy** usług komunikacyjnych platformy Azure demonstruje, jak usługi komunikacyjne wywołujące bibliotekę klienta sieci Web mogą być używane do tworzenia środowiska wywołującego grupę.

W tym przykładowym samouczku szybki start dowiesz się, jak działa przykład przed uruchomieniem przykładu na komputerze lokalnym. Następnie zainstalujemy przykład na platformie Azure przy użyciu własnych zasobów usług Azure Communications Services.

## <a name="download-code"></a>Pobieranie kodu

Znajdź końcowy kod dla tego przewodnika Szybki Start w witrynie [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero).

## <a name="overview"></a>Omówienie

Przykład zawiera aplikację po stronie klienta i aplikację po stronie serwera. **Aplikacja po stronie klienta** to aplikacja sieci Web do reagowania/Redux, która korzysta z platformy interfejsu użytkownika Fluent firmy Microsoft. Ta aplikacja wysyła żądania do ASP.NET Core **aplikacji po stronie serwera** , która pomaga aplikacji po stronie klienta połączyć się z platformą Azure.

Oto jak wygląda przykład:

:::image type="content" source="../media/calling/landing-page.png" alt-text="Zrzut ekranu przedstawiający stronę docelową przykładowej aplikacji.":::

Po naciśnięciu przycisku "Rozpocznij połączenie" aplikacja sieci Web pobierze token dostępu użytkownika z aplikacji po stronie serwera. Token ten jest następnie używany do łączenia aplikacji klienta z usługami Azure Communications Services. Po pobraniu tokenu zostanie wyświetlony monit o określenie aparatu i mikrofonu, który ma być używany. Można wyłączyć lub włączyć urządzenia z kontrolkami przełączania:

:::image type="content" source="../media/calling/pre-call.png" alt-text="Zrzut ekranu przedstawiający ekran przed wywołaniem przykładowej aplikacji.":::

Po skonfigurowaniu wyświetlanej nazwy i urządzeń można przyłączyć się do sesji wywołania. Zostanie wyświetlona główna Kanwa wywołań, w której działa podstawowe środowisko wywoływania.

:::image type="content" source="../media/calling/main-app.png" alt-text="Zrzut ekranu przedstawiający ekran główny przykładowej aplikacji.":::

Składniki głównego ekranu wywołującego:

- **Galeria multimediów**: Główny etap pokazujący uczestników. Jeśli Uczestnik ma włączony aparat fotograficzny, w tym miejscu są wyświetlane ich kanały wideo. Każdy uczestnik ma pojedynczy kafelek, który pokazuje ich nazwę wyświetlaną i strumień wideo (gdy istnieje)
- **Nagłówek**: to miejsce, w którym znajdują się podstawowe kontrolki wywołania, aby przełączać ustawienia i pasek boczny uczestnika, włączać lub wyłączać i mieszać zawartość ekranu oraz pozostawiać wywołanie.
- **Pasek boczny**: to miejsce, w którym informacje o uczestnikach i ustawieniach są wyświetlane po przełączeniu przy użyciu kontrolek w nagłówku. Składnik może zostać odrzucony przy użyciu "X" w prawym górnym rogu. Na pasku bocznym uczestników zostanie wyświetlona lista uczestników i link pozwalający zaprosić więcej użytkowników do rozmowy. Pasek boczny ustawienia umożliwia skonfigurowanie ustawień mikrofonu i aparatu.

Poniżej znajdziesz więcej informacji na temat wymagań wstępnych i kroków związanych z konfigurowaniem przykładu.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 i nowsze)](https://nodejs.org/en/download/)
- [Visual Studio (2019 i nowsze)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Pamiętaj, aby zainstalować wersję odpowiadającą wystąpieniu programu Visual studio, 32 vs 64 bit)
- Zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../quickstarts/create-communication-resource.md). Musisz zarejestrować **Parametry połączenia** zasobu dla tego przewodnika Szybki Start.

## <a name="locally-deploy-the-service--client-applications"></a>Lokalne wdrażanie usługi & aplikacji klienckich

Przykładowa Grupa wywołująca to zasadniczo dwie aplikacje: ClientApp i Service.NET.

Jeśli chcemy wdrożyć lokalnie, musimy uruchomić obie aplikacje. Gdy aplikacja serwera zostanie odwiedzana z przeglądarki, użyje lokalnie wdrożonego ClientApp do obsługi środowiska użytkownika.

Możesz przetestować przykład lokalnie, otwierając wiele sesji przeglądarki z adresem URL wywołania w celu symulowania wywołania z wieloma użytkownikami.

### <a name="before-running-the-sample-for-the-first-time"></a>Przed uruchomieniem przykładu po raz pierwszy

1. Otwórz wystąpienie programu PowerShell, terminalu systemu Windows, wiersza polecenia lub równoważne i przejdź do katalogu, do którego chcesz sklonować przykład.
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. Pobierz `Connection String` z Azure Portal. Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Tworzenie zasobów komunikacji z platformą Azure](../../quickstarts/create-communication-resource.md).
4. Po otrzymaniu `Connection String` Dodaj parametry połączenia do **wywołania/appsetting.jsw** pliku znalezionym w folderze Service .NET. Wprowadź parametry połączenia w zmiennej: `ResourceConnectionString` .

### <a name="local-run"></a>Uruchamianie lokalne

1. Przejdź do folderu wywołującego i Otwórz `Calling.csproj` rozwiązanie w programie Visual Studio.
2. Uruchom `Calling` projekt. Przeglądarka zostanie otwarta pod adresem `localhost:5001` .

## <a name="publish-the-sample-to-azure"></a>Publikuj przykład na platformie Azure

1. Kliknij prawym przyciskiem myszy `Calling` projekt i wybierz polecenie Publikuj.
2. Utwórz nowy profil publikowania i wybierz subskrypcję platformy Azure.
3. Przed opublikowaniem Dodaj parametry połączenia z `Edit App Service Settings` i wypełnij `ResourceConnectionString` jako klucz i podaj parametry połączenia (skopiowane z appsettings.json) jako wartość.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

>[!div class="nextstepaction"]
>[Pobierz przykład z witryny GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z [pomocą biblioteki klienta wywołującego](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Dowiedz się więcej o [sposobie wywoływania programu](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Materiały uzupełniające

- [Azure Communication GitHub](https://github.com/Azure/communication) — Znajdź więcej przykładów i informacji na oficjalnej stronie GitHub
- [Redux](https://redux.js.org/) — zarządzanie stanem po stronie klienta
- [FluentUI](https://aka.ms/fluent-ui) — Biblioteka interfejsu użytkownika zasilanego przez firmę Microsoft
- [Reagowanie](https://reactjs.org/) — Biblioteka do tworzenia interfejsów użytkownika
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) — platforma do tworzenia aplikacji sieci Web
