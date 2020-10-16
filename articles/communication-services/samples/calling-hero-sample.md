---
title: Połączenia grupowe — przykład dużego obrazu
titleSuffix: An Azure Communication Services sample overview
description: Omówienie wywoływania przykładu Hero przy użyciu usług Azure Communication Services, aby umożliwić deweloperom dowiedzieć się więcej o wewnętrznych działaniach przykładu.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: cfb6a7ba10c388cadb19268ee1431fe523a0cfa4
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126330"
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
> [Pobierz przykład z witryny GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero)

## <a name="overview"></a>Omówienie

Przykład zawiera aplikację po stronie klienta i aplikację po stronie serwera. **Aplikacja po stronie klienta** to aplikacja sieci Web do reagowania/Redux, która korzysta z platformy interfejsu użytkownika Fluent firmy Microsoft. Ta aplikacja wysyła żądania do ASP.NET Core **aplikacji po stronie serwera** , która pomaga aplikacji po stronie klienta połączyć się z platformą Azure. 

Oto jak wygląda przykład:

:::image type="content" source="./media/calling/landing-page.png" alt-text="Zrzut ekranu przedstawiający stronę docelową przykładowej aplikacji.&quot;:::

Po naciśnięciu przycisku &quot;Rozpocznij połączenie" aplikacja sieci Web pobierze token dostępu użytkownika z aplikacji po stronie serwera. Token ten jest następnie używany do łączenia aplikacji klienta z usługami Azure Communications Services. Po pobraniu tokenu zostanie wyświetlony monit o określenie aparatu i mikrofonu, który ma być używany. Można wyłączyć lub włączyć urządzenia z kontrolkami przełączania:

:::image type="content" source="./media/calling/pre-call.png" alt-text="Zrzut ekranu przedstawiający stronę docelową przykładowej aplikacji.&quot;:::

Po naciśnięciu przycisku &quot;Rozpocznij połączenie":::

Po skonfigurowaniu wyświetlanej nazwy i urządzeń można przyłączyć się do sesji wywołania. Teraz zobaczysz główną kanwę wywołań, w której działa podstawowe środowisko wywoływania.

:::image type="content" source="./media/calling/main-app.png" alt-text="Zrzut ekranu przedstawiający stronę docelową przykładowej aplikacji.&quot;:::

Po naciśnięciu przycisku &quot;Rozpocznij połączenie" w prawym górnym rogu. Na pasku bocznym uczestników zostanie wyświetlona lista uczestników i link pozwalający zaprosić więcej użytkowników do rozmowy. Pasek boczny ustawienia umożliwia skonfigurowanie ustawień mikrofonu i aparatu.

Poniżej znajdziesz więcej informacji na temat wymagań wstępnych i kroków związanych z konfigurowaniem przykładu.

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 i nowsze)](https://nodejs.org/en/download/)
- [Visual Studio (2019 i nowsze)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Pamiętaj, aby zainstalować wersję odpowiadającą wystąpieniu programu Visual studio, 32 vs 64 bit)
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../quickstarts/create-communication-resource.md). Musisz zarejestrować **Parametry połączenia** zasobu dla tego przewodnika Szybki Start.

## <a name="locally-deploy-the-service--client-applications"></a>Lokalne wdrażanie usługi & aplikacji klienckich

Przykładowa Grupa wywołująca to zasadniczo dwie aplikacje: ClientApp i Service.NET.

Jeśli chcemy wdrożyć lokalnie, musimy uruchomić obie aplikacje. Gdy aplikacja serwera zostanie odwiedzana z przeglądarki, użyje lokalnie wdrożonego ClientApp do obsługi środowiska użytkownika.

Możesz przetestować przykład lokalnie, otwierając wiele sesji przeglądarki z adresem URL wywołania w celu symulowania wywołania z wieloma użytkownikami.

## <a name="before-running-the-sample-for-the-first-time"></a>Przed uruchomieniem przykładu po raz pierwszy

1. Otwórz wystąpienie programu PowerShell, terminalu systemu Windows, wiersza polecenia lub równoważne i przejdź do katalogu, do którego chcesz sklonować przykład.
2. `git clone https://github.com/Azure/Communication.git`
3. Pobierz `Connection String` z Azure Portal. Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Tworzenie zasobów usługi Azure Communication](../quickstarts/create-communication-resource.md)
4. Po otrzymaniu `Connection String` Dodaj parametry połączenia do **wywołania/appsetting.jsw** pliku znalezionym w folderze Service .NET. Wprowadź parametry połączenia w zmiennej: `ResourceConnectionString` .

### <a name="local-run"></a>Uruchomienie lokalne

1. Przejdź do folderu wywołującego i Otwórz `Calling.csproj` rozwiązanie w programie Visual Studio
2. Uruchom `Calling` projekt. Przeglądarka zostanie otwarta przy użyciu nazwy localhost: 5001

#### <a name="troubleshooting"></a>Rozwiązywanie problemów

- Rozwiązanie nie kompiluje się; zgłasza błędy podczas instalacji/kompilacji NPM.

   Spróbuj wyczyścić/skompilować projekty.

## <a name="publish-the-sample-to-azure"></a>Publikuj przykład na platformie Azure

1. Kliknij prawym przyciskiem myszy `Calling` projekt i wybierz polecenie Publikuj.
2. Utwórz nowy profil publikowania i wybierz subskrypcję platformy Azure.
3. Przed opublikowaniem Dodaj parametry połączenia z `Edit App Service Settings` i wypełnij `ResourceConnectionString` jako klucz i podaj parametry połączenia (skopiowane z appsettings.json) jako wartość.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

>[!div class="nextstepaction"] 
>[Pobierz przykład z witryny GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z [pomocą biblioteki klienta wywołującego](../quickstarts/voice-video-calling/calling-client-samples.md)
- Informacje o [wywoływaniu możliwości biblioteki klienta](../quickstarts/voice-video-calling/calling-client-samples.md)
- Dowiedz się więcej o [sposobie wywoływania programu](../concepts/voice-video-calling/about-call-types.md)

## <a name="additional-reading"></a>Materiały uzupełniające

- [Azure Communication GitHub](https://github.com/Azure/communication) — Znajdź więcej przykładów i informacji na oficjalnej stronie GitHub
- [Redux](https://redux.js.org/) — zarządzanie stanem po stronie klienta
- [FluentUI](https://aka.ms/fluent-ui) — Biblioteka interfejsu użytkownika zasilanego przez firmę Microsoft
- [Reagowanie](https://reactjs.org/) — Biblioteka do tworzenia interfejsów użytkownika
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) — platforma do tworzenia aplikacji sieci Web
