---
title: Azure Service Bus sesji komunikatów | Microsoft Docs
description: W tym artykule wyjaśniono, jak używać sesji w celu umożliwienia wspólnej i uporządkowanej obsługi niepowiązanych sekwencji powiązanych komunikatów.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: e22dfb2aa7372a227f70fd2bfa8f72d2161cda17
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750756"
---
# <a name="message-sessions"></a>Sesje komunikatów
Microsoft Azure Service Bus umożliwiają wspólną i uporządkowaną obsługę niepowiązanych sekwencji powiązanych komunikatów. Sesje mogą być używane we **wzorcach pierwszy na w/wy (FIFO)** i **żądanie-odpowiedź.** W tym artykule pokazano, jak używać sesji do implementowania tych wzorców podczas korzystania z Service Bus. 

> [!NOTE]
> Warstwa podstawowa Service Bus nie obsługuje sesji. Warstwy Standardowa i Premium obsługują sesje. Aby uzyskać informacje o różnicach między tymi warstwami, [zobacz Service Bus cennik.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="first-in-first-out-fifo-pattern"></a>Wzorzec fifo (pierwszy na wejściowy, pierwszy na wy)
Aby zrealizować gwarancję FIFO w Service Bus, użyj sesji. Service Bus nie jest nakazowe dotyczące charakteru relacji między komunikatami, a także nie definiuje konkretnego modelu do określania, gdzie rozpoczyna się lub kończy sekwencja komunikatów.

Każdy nadawca może utworzyć sesję podczas przesyłania komunikatów  do tematu lub kolejki, ustawiając właściwość identyfikatora sesji na jakiś identyfikator zdefiniowany przez aplikację, który jest unikatowy dla sesji. Na poziomie protokołu AMQP 1.0 ta wartość jest mapowa na *właściwość group-id.*

W kolejkach lub subskrypcjach z uwzględnieniem sesji sesje istnieją, gdy istnieje co najmniej jeden komunikat z identyfikatorem sesji. Gdy sesja już istnieje, nie ma zdefiniowanego czasu ani interfejsu API dla czasu wygaśnięcia lub zniknięcia sesji. Teoretycznie można odbierać komunikat dla sesji dzisiaj, następnego komunikatu za rok, a jeśli identyfikator sesji jest taki sam z perspektywy Service Bus czasu.

Zazwyczaj jednak aplikacja ma jasne informacje o tym, gdzie rozpoczyna się i kończy zestaw powiązanych komunikatów. Service Bus nie ustawia żadnych określonych reguł. Na przykład aplikacja może ustawić właściwość **Label** dla pierwszego komunikatu, aby uruchomić **,** dla komunikatów pośrednich na zawartość **i** dla ostatniego komunikatu na **koniec**. Względną pozycję komunikatów zawartości można obliczyć jako bieżący komunikat *SequenceNumber* delta z komunikatu startowego  *SequenceNumber*.

> [!IMPORTANT]
> Gdy sesje są włączone w kolejce lub  subskrypcji, aplikacje klienckie nie mogą już wysyłać/odbierać zwykłych komunikatów. Wszystkie komunikaty muszą być wysyłane jako część sesji (przez ustawienie identyfikatora sesji) i odbierane przez zaakceptowanie sesji.

Interfejsy API dla sesji istnieją na klientach kolejek i subskrypcji. Istnieje model imperatywny, który kontroluje czas odbierania sesji i komunikatów, oraz model oparty na programie obsługi, który ukrywa złożoność zarządzania pętlą odbierania. 

W przypadku przykładów użyj linków w [sekcji Następne](#next-steps) kroki. 

### <a name="session-features"></a>Funkcje sesji

Sesje zapewniają współbieżne de multipleksowanie przeplatanych strumieni komunikatów przy zachowaniu i zagwarantowaniu uporządkowanego dostarczania.

![Diagram przedstawiający sposób, w jaki funkcja Sesje zachowuje zamówione dostarczanie.][1]

Odbiornik sesji jest tworzony przez klienta akceptującego sesję. Gdy sesja jest akceptowana i utrzymywana przez klienta, klient ma wyłączną blokadę dla wszystkich komunikatów z identyfikatorem sesji tej sesji **w** kolejce lub subskrypcji. Będzie ona również przechowywać blokady na wyłączność dla wszystkich komunikatów z **identyfikatorem sesji,** który zostanie przybędą później.

Blokada jest zwalniana po wywołaniu powiązanych metod zamknięcia na odbiorniku lub po wygaśnięciu blokady. Istnieją również metody na odbiorniku w celu odnowienia blokad. Zamiast tego można użyć funkcji automatycznego odnawiania blokady, w której można określić czas trwania odnawiania blokady. Blokada sesji powinna być traktowana jak blokada na wyłączność pliku, co oznacza, że aplikacja powinna zamknąć sesję, gdy tylko nie będzie już jej wymagać i/lub nie oczekuje żadnych dalszych komunikatów.

Gdy wiele równoczesnych odbiorników ściąga z kolejki, komunikaty należące do określonej sesji są wysyłane do określonego odbiornika, który aktualnie przechowuje blokadę dla tej sesji. Dzięki tej operacji strumień komunikatów z przeplotem w jednej kolejce lub subskrypcji jest dokładnie de multipleksowany do różnych odbiorników, a te odbiorniki mogą również żyć na różnych maszynach klienckich, ponieważ zarządzanie blokadami odbywa się po stronie usługi, wewnątrz Service Bus.

Na poprzedniej ilustracji przedstawiono trzy odbiorniki sesji współbieżnych. Jedna sesja z = 4 nie ma aktywnego klienta, który jest właścicielem, co oznacza, że żadne komunikaty nie `SessionId` są dostarczane z tej określonej sesji. Sesja działa na wiele sposobów, takich jak kolejka podrzędna.

Blokada sesji utrzymywana przez odbiornik sesji jest elementem blokowym dla blokad komunikatów używanych przez tryb rozliczenia *peek-lock.* Tylko jeden odbiornik może mieć blokadę sesji. Odbiornik może mieć wiele komunikatów w locie, ale komunikaty będą odbierane w kolejności. Porzucenie komunikatu powoduje, że ten sam komunikat będzie ponownie obsługiwany przy następnej operacji odbierania.

### <a name="message-session-state"></a>Stan sesji komunikatów

Gdy przepływy pracy są przetwarzane w systemach w chmurze o wysokiej dostępności na dużą skalę, procedura obsługi przepływu pracy skojarzona z określoną sesją musi być w stanie odzyskać dane po nieoczekiwanych awariach i wznowić częściowo ukończone prace nad innym procesem lub maszyną, na której rozpoczęto pracę.

Funkcja stanu sesji umożliwia adnotację zdefiniowaną przez aplikację sesji komunikatów wewnątrz brokera, dzięki czemu zarejestrowany stan przetwarzania względem tej sesji staje się natychmiast dostępny, gdy sesja zostanie uzyskana przez nowy procesor.

Z perspektywy Service Bus stan sesji komunikatów jest nieprzezroczystym obiektem binarnym, który może przechowywać dane o rozmiarze jednego komunikatu, czyli 256 KB dla standardu Service Bus i 1 MB dla Service Bus Premium. Stan przetwarzania względem sesji może być utrzymywany wewnątrz stanu sesji lub stan sesji może wskazać lokalizację magazynu lub rekord bazy danych, który przechowuje takie informacje.

Metody zarządzania stanem sesji, SetState i GetState, można znaleźć w obiekcie odbiornika sesji. Sesja, która wcześniej nie miała stanu sesji, zwraca odwołanie o wartości null dla getstate. Wcześniej ustawiony stan sesji można wyczyścić, przekazując wartość null do metody SetState odbiornika.

Stan sesji pozostaje tak długo, jak długo nie jest wyczyszowany (zwraca wartość **null),** nawet jeśli są używane wszystkie komunikaty w sesji.

Stan sesji przechowywanej w kolejce lub w subskrypcji wlicza się do limitu przydziału magazynu tej jednostki. Po zakończeniu sesji aplikacji zaleca się oczyszczenie jej stanu zachowanego w celu uniknięcia zewnętrznych kosztów zarządzania.

### <a name="impact-of-delivery-count"></a>Wpływ liczby dostaw

Definicja liczby dostaw na komunikat w kontekście sesji różni się nieco od definicji w przypadku braku sesji. Poniżej znajduje się tabela podsumowująca, kiedy liczba dostaw jest zwiększana.

| Scenariusz | Czy liczba dostaw komunikatu zwiększa się |
|----------|---------------------------------------------|
| Sesja jest akceptowana, ale blokada sesji wygasa (z powodu prze upływu limitu czasu) | Tak |
| Sesja jest akceptowana, komunikaty w ramach sesji nie są ukończone (nawet jeśli są zablokowane) i sesja jest zamykana | Nie |
| Sesja jest akceptowana, komunikaty są ukończone, a następnie sesja jest jawnie zamknięta | Nie ma (jest to standardowy przepływ. W tym miejscu komunikaty są usuwane z sesji) |

## <a name="request-response-pattern"></a>Wzorzec żądania i odpowiedzi
Wzorzec [żądanie-odpowiedź](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) to dobrze ustalony wzorzec integracji, który umożliwia aplikacji nadawcy wysyłanie żądania i umożliwia odbiorcy prawidłowe wysłanie odpowiedzi z powrotem do aplikacji nadawcy. Ten wzorzec zwykle wymaga krótkotrwałej kolejki lub tematu, na który aplikacja może wysyłać odpowiedzi. W tym scenariuszu sesje zapewniają proste, alternatywne rozwiązanie o porównywalnej semantyce. 

Wiele aplikacji może wysyłać żądania do jednej kolejki żądań z określonym parametrem nagłówka ustawionym w celu unikatowego identyfikowania aplikacji nadawcy. Aplikacja odbiorcy może przetwarzać żądania wysyłane do kolejki i wysyłać odpowiedzi w kolejce z włączoną sesją, ustawiając identyfikator sesji na unikatowy identyfikator wysłany przez nadawcę w komunikacie żądania. Aplikacja, która wysłała żądanie, może następnie odbierać komunikaty dotyczące określonego identyfikatora sesji i poprawnie przetwarzać odpowiedzi.

> [!NOTE]
> Aplikacja, która wysyła początkowe żądania, powinna wiedzieć o identyfikatorze sesji i używać go do akceptowania sesji, tak aby sesja, w której oczekuje odpowiedzi, została zablokowana. Dobrym pomysłem jest użycie identyfikatora GUID, który jednoznacznie identyfikuje wystąpienie aplikacji jako identyfikator sesji. Nie powinna być określona procedura obsługi sesji ani limit czasu odbiornika sesji dla kolejki, aby upewnić się, że odpowiedzi są dostępne do zablokowania i przetworzenia przez określonych odbiorców.

## <a name="next-steps"></a>Następne kroki
Sesje komunikatów można włączyć podczas tworzenia kolejki przy użyciu programu Azure Portal, programu PowerShell, interfejsu wiersza polecenia, Resource Manager szablonu, platform .NET, Java, Python i JavaScript. Aby uzyskać więcej informacji, zobacz [Włączanie sesji komunikatów.](enable-message-sessions.md) 

Wypróbuj przykłady w wybranego języku, aby poznać Azure Service Bus funkcji. 

- [Azure Service Bus biblioteki klienta dla języka Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus biblioteki klienta dla języka Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus biblioteki klienta dla języka JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus biblioteki klienta dla języka TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Przykłady dla platformy .NET Azure.Messaging.ServiceBus](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Poniżej znajdują się przykłady dla starszych bibliotek klienckich .NET i Java:
- [Przykłady microsoft.Azure.ServiceBus dla platformy .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [przykłady azure-servicebus dla języka Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

[1]: ./media/message-sessions/sessions.png

