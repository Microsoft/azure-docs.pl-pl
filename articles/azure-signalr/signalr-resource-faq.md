---
title: Często zadawane pytania dotyczące usługi Azure SignalR Service
description: Uzyskaj szybki dostęp do często zadawanych pytań dotyczących usługi Azure Signal Service, rozwiązywania problemów i typowych scenariuszy użycia.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 6d104e41a0cae906c346e81a26617a9d29795fb3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853285"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service — FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Czy usługa Azure SignalR Service jest gotowa do użycia w środowisku produkcyjnym?

Tak.
Nasze zawiadomienie o ogólnej dostępności można znaleźć w artykule [Azure SignalR Service now generally available](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/) (Usługa Azure SignalR Service jest teraz ogólnie dostępna). 

[Biblioteka SignalR platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/introduction) jest w pełni obsługiwana.

Obsługa biblioteki SignalR platformy ASP.NET znajduje się nadal w *publicznej wersji zapoznawczej*. Tu można znaleźć [przykładowy kod](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Połączenie klienta zostaje zamknięte z komunikatem o błędzie „Brak dostępnych serwerów”. Co to oznacza?

Ten błąd występuje tylko w sytuacji, w której klienci wysyłają komunikaty do usługi SignalR Service.

Jeśli nie masz żadnego serwera aplikacji i używasz tylko interfejsu API REST usługi SignalR Service, to zachowanie jest **zgodne z projektem**.
W architekturze bezserwerowej połączenia klienta działają w trybie **NASŁUCHIWANIE** i komunikaty do usługi SignalR Service nie będą wysyłane.
Dowiedz się więcej na temat [interfejsu API REST](./signalr-quickstart-rest-api.md).

Jeśli masz serwery aplikacji, ten komunikat o błędzie oznacza, że żaden serwer aplikacji nie jest podłączony do wystąpienia usługi SignalR Service.

Możliwe przyczyny to:
- Żaden serwer aplikacji nie jest połączony z usługą SignalR Service. Sprawdź dzienniki serwera aplikacji pod kątem możliwych błędów połączeń. Ten przypadek jest rzadki przy ustawieniu wysokiej dostępności przy użyciu kilku serwerów aplikacji.
- Występują problemy z połączeniem z wystąpieniami usługi SignalR Service. Ten problem jest przejściowy i zostanie automatycznie naprawiony.
Jeśli utrzymuje się dłużej niż przez godzinę, [otwórz problem w serwisie GitHub](https://github.com/Azure/azure-signalr/issues/new) lub [utwórz żądanie obsługi na platformie Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Czy w przypadku wielu serwerów aplikacji komunikaty klienckie są wysyłane do wszystkich serwerów czy tylko do jednego z nich?

Między klientem i serwerem aplikacji występuje mapowanie jeden do jednego. Komunikaty od jednego klienta są zawsze wysyłane do tego samego serwera aplikacji.

Mapowanie między klientem i serwerem aplikacji zostanie zachowane do momentu odłączenia klienta lub serwera aplikacji.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Jeśli jeden z moich serwerów aplikacji nie działa, jak mogę się o tym dowiedzieć i uzyskać powiadomienie?

Usługa SignalR Service monitoruje sygnały pulsu z serwerów aplikacji.
Jeśli sygnały pulsu nie są odbierane przez określony czas, serwer aplikacji jest uznawany za działający w trybie offline. Wszystkie połączenia klienta mapowane na ten serwer aplikacji zostaną rozłączone.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Dlaczego mój niestandardowy `IUserIdProvider` zgłasza wyjątek w przypadku przejścia z zestawu SDK biblioteki SignalR dla platformy ASP.NET Core do zestawu SDK usługi Azure SignalR Service?

Przy wywoływaniu `IUserIdProvider` parametr `HubConnectionContext context` dla zestawu SDK biblioteki SignalR dla platformy ASP.NET Core jest inny niż dla zestawu SDK usługi Azure SignalR Service.

W bibliotece SignalR platformy ASP.NET Core parametr `HubConnectionContext context` zawiera kontekst z prawidłowymi wartościami dla wszystkich właściwości połączenia fizycznego klienta.

W zestawie SDK usługi Azure SignalR Service parametr `HubConnectionContext context` zawiera kontekst połączenia logicznego klienta. Fizyczne połączenie klienta jest podłączone do wystąpienia usługi SignalR Service, dlatego udostępniana jest tylko ograniczona liczba właściwości.

Obecnie tylko parametry `HubConnectionContext.GetHttpContext()` i `HubConnectionContext.User` są dostępne.
Kod źródłowy można sprawdzić [tutaj](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Czy mogę skonfigurować transport dostępny w usłudze SignalR Service poprzez skonfigurowanie go po stronie serwera przy użyciu biblioteki SignalR platformy ASP.NET Core? Na przykład wyłączyć transport WebSocket?

Nie.

Usługa Azure SignalR Service oferuje wszystkie trzy rodzaje transportu, które domyślnie obsługuje biblioteka SignalR platformy ASP.NET Core. Tego nie można konfigurować. Usługa SignalR Service będzie obsługiwać połączenia i transport dla wszystkich połączeń klienckich.

Możesz skonfigurować transport po stronie klienta, zgodnie z opisem umieszczonym [tutaj](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Jaki jest znaczenie metryk, takich jak liczba komunikatów lub liczba połączeń pokazane w Azure Portal? Jakiego typu agregacji należy wybrać?

Szczegółowe informacje na temat sposobu obliczania tych metryk są dostępne [tutaj](signalr-concept-messages-and-connections.md).

W bloku przeglądu zasobów usługi Azure Signal Service został już wybrany odpowiedni typ agregacji. A jeśli przejdziesz do bloku metryk, możesz w [tym miejscu](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) zastosować typ agregacji jako odwołanie.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Czym jest znaczenie trybu usługi `Default` / `Serverless` / `Classic` ? Jak mogę wybrać?

Środka
* `Default` tryb *wymaga* serwera centrum. W tym trybie usługa Azure Signal kieruje ruch klienta do podłączonych połączeń serwera Hub. Usługa Azure Signal testuje podłączony serwer centrum. Jeśli podłączony serwer centralny nie zostanie znaleziony, usługa Azure Signal odrzuca przychodzące połączenia klientów. W tym trybie można także użyć **interfejsu API zarządzania** , aby zarządzać połączonymi klientami bezpośrednio za pomocą usługi Azure signaler.
* `Serverless` tryb *nie* zezwala na żadne połączenie z serwerem, czyli spowoduje odrzucenie wszystkich połączeń z serwerem. Wszyscy klienci muszą mieć tryb bezserwerowy. Klienci łączą się z usługą Azure sygnalizujący, a użytkownicy zazwyczaj używają technologii bezserwerowych, takich jak **Azure Function** , do obsługi logiki centrum. Zobacz [prosty przykład](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) , który używa trybu bezserwerowego usługi Azure Signal.
* `Classic` tryb jest stanem mieszanym. Gdy koncentrator ma połączenie z serwerem, nowy klient zostanie rozesłany do serwera Hub, w przeciwnym razie klient przejdzie do trybu bezserwerowego.

  Może to spowodować jakiś problem, na przykład w przypadku utraty wszystkich połączeń z serwerem, niektórzy klienci będą wprowadzać tryb bez serwera zamiast kierować do serwera Hub.

Określając
1. Brak serwera centrum, wybierz opcję `Serverless` .
1. Wszystkie centra mają serwery centrów, wybierz opcję `Default` .
1. Niektóre centra mają serwery Hub, inne nie, wybierają `Classic` się, ale może to spowodować jakiś problem. lepszym sposobem jest utworzenie dwóch wystąpień, jeden z `Serverless` drugim `Default` .

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Jakie są różnice dotyczące funkcji w przypadku korzystania z usługi Azure Signal dla sygnalizującego ASP.NET?
W przypadku korzystania z usługi Azure sygnalizacyjnym niektóre interfejsy API i funkcje sygnalizujące ASP.NET nie są już obsługiwane:
- Możliwość przekazywania dowolnego stanu między klientami a centrum (często wywoływane `HubState` ) nie jest obsługiwana w przypadku korzystania z usługi Azure Signal
- `PersistentConnection` Klasa nie jest jeszcze obsługiwana w przypadku korzystania z usługi Azure Signal
- **Transport ramki bez ograniczeń** nie jest obsługiwany w przypadku korzystania z usługi Azure Signal
- Usługa Azure sygnalizująca nie odtwarza już komunikatów wysyłanych do klienta, gdy klient jest w trybie offline
- W przypadku korzystania z usługi Azure Signal ruch dla jednego połączenia klienta jest zawsze kierowany (alias. Program **Sticker**) do jednego wystąpienia serwera aplikacji na czas trwania połączenia

Pomoc techniczna dla sygnalizującego ASP.NET jest ukierunkowana na zgodność, więc nie wszystkie nowe funkcje z ASP.NET Core sygnalizujący są obsługiwane. Na przykład **MessagePack**, **Streaming**itp., są dostępne tylko dla aplikacji dla sygnałów ASP.NET Core.

Usługę sygnalizującą można skonfigurować dla innego trybu usługi: `Classic` / `Default` / `Serverles` s. W tym obsłudze ASP.NET `Serverless` tryb nie jest obsługiwany. Interfejs API REST płaszczyzny danych również nie jest obsługiwany.

## <a name="where-do-my-data-reside"></a>Gdzie znajdują się moje dane?

Usługa Azure Signal Service działa jako usługa procesora danych. Nie będzie ona przechowywać żadnej zawartości klienta i dane zamieszkania są zadzielone przez zaprojektowanie. Jeśli używasz usługi Azure Signal Service wraz z innymi usługami platformy Azure, takimi jak Azure Storage for Diagnostics, zobacz [tutaj](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) , aby uzyskać wskazówki dotyczące przechowywania danych znajdujących się w regionach platformy Azure.
