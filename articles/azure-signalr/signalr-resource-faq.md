---
title: Często zadawane pytania dotyczące usługi Azure SignalR Service
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Azure sygnalizującej, w tym Rozwiązywanie problemów i typowe scenariusze użycia.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 910de9efbd132fb98a0c4bd596867800f65f5ad5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150963"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service — FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Czy usługa Azure SignalR Service jest gotowa do użycia w środowisku produkcyjnym?

Tak, zarówno pomoc techniczna dla [sygnałów ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/signalr) , jak i [sygnalizującego ASP.NET](/aspnet/signalr/overview/getting-started/introduction-to-signalr) jest ogólnie dostępna.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Czy w przypadku wielu serwerów aplikacji komunikaty klienckie są wysyłane do wszystkich serwerów czy tylko do jednego z nich?

Istnieje mapowanie jeden do jednego między klientem a serwerem aplikacji. Komunikaty od jednego klienta są zawsze wysyłane do tego samego serwera aplikacji.

Mapowanie jest zachowywane do momentu odłączenia się klienta lub serwera aplikacji.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Jeśli jeden z moich serwerów aplikacji nie działa, jak mogę się o tym dowiedzieć i uzyskać powiadomienie?

Usługa Azure Signal monitoruje pulsy z serwerów aplikacji.
Jeśli sygnały pulsu nie są odbierane przez określony czas, serwer aplikacji jest uznawany za działający w trybie offline. Wszystkie połączenia klienta mapowane na ten serwer aplikacji zostaną rozłączone.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Dlaczego moje niestandardowe `IUserIdProvider` zgłoszenie wyjątku podczas przełączania z zestawu sdk ASP.NET Core sygnalizującego do zestawu SDK usługi Azure signaler?

Parametr `HubConnectionContext context` jest różny między zestawem sdk ASP.NET Core sygnalizujący a zestawem SDK usługi Azure Signal, gdy `IUserIdProvider` jest wywoływana.

W bibliotece SignalR platformy ASP.NET Core parametr `HubConnectionContext context` zawiera kontekst z prawidłowymi wartościami dla wszystkich właściwości połączenia fizycznego klienta.

W zestawie SDK usługi Azure Signal Agent `HubConnectionContext context` jest kontekstem z logicznego połączenia klienta. Klient fizyczny jest połączony z wystąpieniem usługi Azure sygnalizującego, więc podano tylko ograniczoną liczbę właściwości.

Obecnie tylko parametry `HubConnectionContext.GetHttpContext()` i `HubConnectionContext.User` są dostępne.
Możesz [sprawdzić kod źródłowy](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Czy można skonfigurować Transporty dostępne w usłudze Azure Signal Service po stronie serwera za pomocą usługi ASP.NET Core Signaler? Czy na przykład można wyłączyć transport WebSocket?

Nie.

Usługa Azure SignalR Service oferuje wszystkie trzy rodzaje transportu, które domyślnie obsługuje biblioteka SignalR platformy ASP.NET Core. Nie można go konfigurować. Usługa Azure Signal Service będzie obsługiwać połączenia i transporty dla wszystkich połączeń klientów.

Transporty po stronie klienta można skonfigurować zgodnie z opisem w [konfiguracji ASP.NET Core](/aspnet/core/signalr/configuration#configure-allowed-transports-1).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Jakie są znaczenie metryk, takich jak liczba komunikatów lub liczba połączeń wyświetlana w Azure Portal? Jakiego typu agregacji należy wybrać?

Szczegółowe informacje o tych metrykach można znaleźć w [komunikatach i połączeniach w usłudze Azure Signal Service](signalr-concept-messages-and-connections.md).

W okienku Przegląd zasobów usługi Azure Signal Service został już wybrany odpowiedni typ agregacji. Jeśli przejdziesz do okienka metryki, możesz zastosować typ agregacji do [komunikatów i połączeń w usłudze Azure Signal Service](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) jako odwołanie.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Co to jest znaczenie dla `Default` `Serverless` trybów usługi, i `Classic` ? Jak mogę wybrać?

W przypadku nowych aplikacji należy użyć tylko domyślnego i bezserwerowego trybu. Główną różnicą jest to, czy masz serwery aplikacji, które ustanawiają połączenia serwera z usługą (np. `AddAzureSignalR()` w celu nawiązania połączenia z usługą). Jeśli tak, użyj trybu domyślnego, w przeciwnym razie użyj trybu bezserwerowego.

Tryb klasyczny został zaprojektowany w celu zapewnienia zgodności z poprzednimi wersjami dla istniejących aplikacji, dlatego nie należy używać go w przypadku nowych aplikacji.

Aby uzyskać więcej informacji na temat trybu usługi w [tym dokumencie](concept-service-mode.md).

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>Czy mogę wysłać komunikat z klienta w trybie bezserwerowym?

Komunikat można wysłać z klienta w przypadku skonfigurowania nadrzędnego w wystąpieniu sygnalizującego. Nadrzędny to zbiór punktów końcowych, które mogą odbierać komunikaty i zdarzenia połączeń z usługi sygnalizującego. Jeśli nie skonfigurowano żadnego strumienia, komunikaty z klienta zostaną zignorowane.

Aby uzyskać więcej informacji na temat nadrzędnego, zobacz [ten dokument](concept-upstream.md).

Nadrzędny jest obecnie w publicznej wersji zapoznawczej.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Czy istnieją jakieś różnice w korzystaniu z usługi Azure Signal Service z ASP.NET Signaler?

Gdy korzystasz z usługi Azure Signal Service, niektóre interfejsy API i funkcje sygnalizującego ASP.NET nie są obsługiwane:
- Możliwość przekazania dowolnego stanu między klientami a centrum (często wywoływanymi `HubState` ) nie jest obsługiwana.
- `PersistentConnection`Klasa nie jest obsługiwana.
- *Transport ramki bez ograniczeń* nie jest obsługiwany.
- Usługa Azure sygnalizująca nie odtwarza już komunikatów wysyłanych do klienta, gdy klient jest w trybie offline.
- Gdy korzystasz z usługi Azure Signal Service, ruch dla jednego połączenia z klientem jest zawsze kierowany (nazywany również *Sticker*) do jednego wystąpienia serwera aplikacji na czas trwania połączenia.

Obsługa usługi ASP.NET Signal jest ukierunkowana na zgodność, więc nie wszystkie nowe funkcje z ASP.NET Core sygnalizujący są obsługiwane. Na przykład *MessagePack* i *Streaming* są dostępne tylko dla aplikacji dla sygnałów ASP.NET Core.

Usługę sygnałów platformy Azure można skonfigurować dla różnych trybów usługi: `Classic` , `Default` , i `Serverless` . `Serverless`Tryb nie jest obsługiwany w przypadku ASP.NET. Interfejs API REST płaszczyzny danych również nie jest obsługiwany.

## <a name="where-does-my-data-reside"></a>Gdzie znajdują się moje dane?

Usługa Azure Signal Service działa jako usługa procesora danych. Nie będzie on przechowywać żadnej zawartości klienta, a dane zamieszkania zostaną dołączone do projektu. Jeśli używasz usługi Azure Signal Service wraz z innymi usługami platformy Azure, takimi jak Azure Storage for Diagnostics, zobacz [ten oficjalny dokument](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) , aby uzyskać wskazówki dotyczące przechowywania danych znajdujących się w regionach platformy Azure.