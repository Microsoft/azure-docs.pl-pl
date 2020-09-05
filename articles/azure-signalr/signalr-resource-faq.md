---
title: Często zadawane pytania dotyczące usługi Azure SignalR Service
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Azure sygnalizującej, w tym Rozwiązywanie problemów i typowe scenariusze użycia.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489565"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service — FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Czy usługa Azure SignalR Service jest gotowa do użycia w środowisku produkcyjnym?

Tak.
Aby uzyskać ogłoszenie o ogólnej dostępności, zobacz [usługa Azure Signal Service jest teraz ogólnie dostępna](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[Biblioteka SignalR platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/introduction) jest w pełni obsługiwana.

Pomoc techniczna dla sygnalizującego ASP.NET jest nadal w *publicznej wersji zapoznawczej*. [Oto przykład kodu](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Połączenie z klientem zostanie zamknięte z komunikatem o błędzie "Brak dostępnego serwera". Co to oznacza?

Ten błąd występuje tylko wtedy, gdy klienci wysyłają komunikaty do usługi Azure Signal Service.

Jeśli nie masz żadnego serwera aplikacji i używasz tylko interfejsu API REST usługi Azure Signal, to zachowanie jest zgodne z *projektem*.
W architekturze bezserwerowym połączenia klientów są w trybie *nasłuchiwania* i nie wysyłają żadnych komunikatów do usługi Azure Signal Service.
Dowiedz [się więcej o interfejsie API REST](./signalr-quickstart-rest-api.md).

Jeśli masz serwery aplikacji, ten komunikat o błędzie oznacza, że żaden serwer aplikacji nie jest połączony z wystąpieniem usługi Azure sygnalizacyjnym.

Możliwe przyczyny to:
- Żaden serwer aplikacji nie jest połączony z usługą Azure Signal Service. Sprawdź dzienniki serwera aplikacji pod kątem możliwych błędów połączeń. Ten przypadek jest rzadki w ustawieniu wysokiej dostępności, które ma więcej niż jeden serwer aplikacji.
- Występują problemy z łącznością z wystąpieniami usługi Azure Signal instance. Ten problem jest przejściowy, a wystąpienia będą automatycznie odzyskiwane.
Jeśli utrzymuje się dłużej niż przez godzinę, [otwórz problem w serwisie GitHub](https://github.com/Azure/azure-signalr/issues/new) lub [utwórz żądanie obsługi na platformie Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

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

Transporty po stronie klienta można skonfigurować zgodnie z opisem w [konfiguracji ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Jakie są znaczenie metryk, takich jak liczba komunikatów lub liczba połączeń wyświetlana w Azure Portal? Jakiego typu agregacji należy wybrać?

Szczegółowe informacje o tych metrykach można znaleźć w [komunikatach i połączeniach w usłudze Azure Signal Service](signalr-concept-messages-and-connections.md).

W okienku Przegląd zasobów usługi Azure Signal Service został już wybrany odpowiedni typ agregacji. Jeśli przejdziesz do okienka metryki, możesz zastosować typ agregacji do [komunikatów i połączeń w usłudze Azure Signal Service](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) jako odwołanie.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Co to jest znaczenie dla `Default` `Serverless` trybów usługi, i `Classic` ? Jak mogę wybrać?

Poniżej przedstawiono informacje o trybach:
* `Default` tryb *wymaga* serwera centrum. W tym trybie usługa Azure sygnalizująca kieruje ruch klienta do podłączonych połączeń serwera Hub. Usługa sygnałów platformy Azure sprawdza połączony serwer centralny. Jeśli usługa nie może znaleźć podłączonego serwera Hub, program odrzuci przychodzące połączenia klientów. W tym trybie można także użyć *interfejsu API zarządzania* , aby zarządzać połączonymi klientami bezpośrednio za pomocą usługi Azure Signal.
* `Serverless` tryb *nie* zezwala na żadne połączenie z serwerem. Oznacza to, że spowoduje to odrzucenie wszystkich połączeń z serwerem. Wszyscy klienci muszą mieć tryb bezserwerowy. Klienci łączą się z usługą Azure Signal Service, a użytkownicy zazwyczaj używają technologii bezserwerowych, takich jak *Azure Functions* do obsługi logiki centrum. [Zobacz prosty przykład](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) , który używa trybu bezserwerowego w usłudze Azure Signal Service.
* `Classic` tryb jest stanem mieszanym. Gdy koncentrator ma połączenie z serwerem, nowy klient zostanie skierowany do serwera centralnego. W przeciwnym razie klient przejdzie w tryb bezserwerowy. 

  Może to spowodować wystąpienie problemu. Na przykład jeśli wszystkie połączenia z serwerem zostaną utracone przez pewien moment, niektórzy klienci będą wprowadzać tryb bez serwera zamiast routingu do serwera Hub.

Poniżej przedstawiono niektóre wskazówki dotyczące wybierania trybu:
- Jeśli nie ma serwera centrum, wybierz opcję `Serverless` .
- Jeśli wszystkie centra mają serwery Hub, wybierz opcję `Default` .
- Jeśli niektóre centra mają serwery Hub, ale inne nie, można wybrać `Classic` , ale może to spowodować problem. Lepszym sposobem jest utworzenie dwóch wystąpień: jeden `Serverless` , a drugi `Default` .

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
