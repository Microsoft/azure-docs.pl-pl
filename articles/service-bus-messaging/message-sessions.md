---
title: Sesje komunikatów Azure Service Bus | Microsoft Docs
description: W tym artykule wyjaśniono, jak używać sesji, aby umożliwić wspólną i uporządkowaną obsługę niepowiązanych sekwencji powiązanych komunikatów.
ms.topic: article
ms.date: 04/12/2021
ms.openlocfilehash: c9a1c4fdccbbc8b38805e23d4895448959126f10
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308486"
---
# <a name="message-sessions"></a>Sesje komunikatów
Sesje Microsoft Azure Service Bus umożliwiają wspólną i uporządkowaną obsługę niepowiązanych sekwencji powiązanych komunikatów. Sesji można używać w wzorcach **First In, First Out (FIFO)** i **Request-Response** . W tym artykule pokazano, jak za pomocą sesji zaimplementować te wzorce przy użyciu Service Bus. 

> [!NOTE]
> Warstwa Podstawowa Service Bus nie obsługuje sesji. W warstwach Standardowa i Premium obsługiwane są sesje. Aby zapoznać się z różnicami między tymi warstwami, zobacz [Cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Wzorzec First-In, First Out (FIFO)
Aby zrealizować gwarancję FIFO w Service Bus, użyj sesji. Service Bus nie jest to opis charakteru relacji między komunikatami, a także nie definiuje konkretnego modelu do określenia, gdzie zostanie uruchomiona lub zakończona sekwencja komunikatów.

Każdy nadawca może utworzyć sesję podczas przesyłania komunikatów do tematu lub kolejki przez ustawienie właściwości **Identyfikator sesji** na określony identyfikator zdefiniowany przez aplikację, który jest unikatowy dla danej sesji. Na poziomie protokołu AMQP 1,0 ta wartość jest mapowana na Właściwość *Group-ID* .

W przypadku kolejek lub subskrypcji z obsługą sesji sesje są dostępne, gdy istnieje co najmniej jeden komunikat z IDENTYFIKATORem sesji. Gdy sesja już istnieje, nie ma zdefiniowanego czasu ani interfejsu API dla momentu wygaśnięcia lub znikania sesji. Teoretycznie można odebrać komunikat dla sesji dzisiaj, następnej wiadomości w czasie roku i jeśli identyfikator sesji jest zgodny, sesja jest taka sama w perspektywie Service Bus.

Zwykle jednak aplikacja ma jasne koncepcje, w której zestaw powiązanych komunikatów zaczyna się i kończy. Service Bus nie ustawi żadnych określonych reguł. Na przykład aplikacja może ustawić właściwość **etykieta** pierwszego komunikatu do **uruchomienia** **, w przypadku** komunikatów pośrednich i dla ostatniego komunikatu do **końca**. Względne położenie komunikatów zawartości można obliczyć jako bieżące *SequenceNumber* różnice **między komunikatami** z *SequenceNumber*.

Aby włączyć tę funkcję, należy ustawić właściwość [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) w kolejce lub subskrypcji za pośrednictwem Azure Resource Manager lub ustawić flagę w portalu. Jest to wymagane przed podjęciem próby użycia pokrewnych operacji interfejsu API.

W portalu można włączyć sesje podczas tworzenia jednostki (kolejki lub subskrypcji), jak pokazano w poniższych przykładach. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Włącz sesję w momencie tworzenia kolejki":::

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Włącz sesję w momencie tworzenia subskrypcji":::


> [!IMPORTANT]
> Po włączeniu sesji dla kolejki lub subskrypcji aplikacje klienckie ***nie mogą już*** wysyłać/odbierać zwykłych komunikatów. Wszystkie komunikaty muszą być wysyłane w ramach sesji (przez ustawienie identyfikatora sesji) i odebrane przez zaakceptowanie sesji.

Interfejsy API dla sesji znajdują się na klientach w kolejkach i subskrypcjach. Istnieje model, który kontroluje czas odbierania sesji i komunikatów oraz model oparty na programie obsługi, który ukrywa złożoność zarządzania pętlą odbierania. 

Aby zapoznać się z przykładami, użyj linków w sekcji [następne kroki](#next-steps) . 

### <a name="session-features"></a>Funkcje sesji

Sesje umożliwiają jednoczesne usuwanie z przeplotu strumieni komunikatów podczas zachowywania i zagwarantowania uporządkowanego dostarczania.

![Diagram przedstawiający sposób zachowywania uporządkowanej dostawy przez funkcję Sessions.][1]

Odbiorca sesji jest tworzony przez klienta akceptującego sesję. Gdy sesja zostanie zaakceptowana i zatrzymywana przez klienta, klient programu przechowuje na wyłączność blokadę wszystkich komunikatów z **identyfikatorem sesji** tej sesji w kolejce lub subskrypcji. Zostanie także zastosowana wyłączne blokady wszystkich komunikatów z **identyfikatorem sesji** , który zostanie przychodzący później.

Blokada jest wydawana po wywołaniu odpowiednich metod zamykania w odbiorniku lub po wygaśnięciu blokady. W odbiorniku są dostępne metody odnawiania blokad. Zamiast tego można użyć funkcji automatycznego odnawiania blokady, w której można określić czas trwania, dla którego ma zostać odnowione blokowanie. Blokada sesji powinna być traktowana jak blokada wyłączna dla pliku, co oznacza, że aplikacja powinna zamykać sesję, gdy tylko nie będą potrzebne, i/lub nie oczekuje żadnych dalszych komunikatów.

Gdy wiele współbieżnych odbiorników pobiera z kolejki, komunikaty należące do określonej sesji są wysyłane do określonego odbiornika, który aktualnie przechowuje blokadę dla danej sesji. W przypadku tej operacji, nieprzechodzący strumień komunikatów w jednej kolejce lub subskrypcji jest usuwany z różnych odbiorników, a odbiorcy mogą również być aktywni na różnych komputerach klienckich, ponieważ zarządzanie blokadą odbywa się po stronie usługi w Service Bus.

Poprzednia ilustracja przedstawia trzy współbieżne odbiorniki sesji. Jedna sesja z programem `SessionId` = 4 nie ma aktywnego klienta będącego właścicielem, co oznacza, że żadne komunikaty nie są dostarczane z tej konkretnej sesji. Sesja działa na wiele sposobów, takich jak Kolejka podrzędna.

Blokada sesji zatrzymywana przez odbiorcę sesji to parasol dla blokad komunikatów używanych przez tryb rozliczania *blokady wglądu* . Tylko jeden odbiornik może mieć blokadę w sesji. Odbiorca może mieć wiele komunikatów w locie, ale komunikaty zostaną odebrane w pożądanej kolejności. Porzucanie komunikatu powoduje ponowne obsłużynie tego samego komunikatu z następną operacją Receive.

### <a name="message-session-state"></a>Stan sesji komunikatu

Gdy przepływy pracy są przetwarzane w systemach w chmurze o wysokiej dostępności, obsługa przepływu pracy skojarzona z konkretną sesją musi mieć możliwość odzyskania po nieoczekiwanych awariach i wznowić pracę częściowo ukończoną w innym procesie lub na maszynie, w której rozpoczęto pracę.

Funkcja stanu sesji umożliwia zdefiniowaną przez aplikację adnotację sesji wiadomości wewnątrz brokera, dzięki czemu stan zapisanego przetwarzania względem tej sesji stanie się natychmiast dostępny po uzyskaniu sesji przez nowy procesor.

Z perspektywy Service Bus stan sesji jest nieprzezroczystym obiektem binarnym, który może przechowywać dane rozmiaru jednego komunikatu, czyli 256 KB dla Service Bus Standard i 1 MB dla Service Bus Premium. Stan przetwarzania odnoszący się do sesji może być przechowywany w stanie sesji lub stan sesji może wskazywać na część lokalizacji magazynu lub rekordu bazy danych, która zawiera takie informacje.

Metody zarządzania stanem sesji, setstate i GetState można znaleźć w obiekcie odbiorcy sesji. Sesja, która nie ma wcześniej stanu sesji, zwraca odwołanie o wartości null dla elementu GetState. Poprzednio ustawiony stan sesji można wyczyścić, przekazując wartość null do metody setstate w odbiorniku.

Stan sesji pozostaje, dopóki nie zostanie wyczyszczony (zwracając **wartość null**), nawet jeśli wszystkie komunikaty w sesji są używane.

Stan sesji w kolejce lub w subskrypcji liczy się na przydział magazynu tego obiektu. Gdy aplikacja zostanie zakończona z sesją, zalecane jest, aby aplikacja mogła oczyścić stan zachowanych, aby uniknąć zewnętrznego kosztu zarządzania.

### <a name="impact-of-delivery-count"></a>Wpływ liczby dostaw

Definicja liczby dostaw na komunikat w kontekście sesji różni się nieco od definicji w przypadku braku sesji. Poniżej przedstawiono tabelę podsumowującą, kiedy licznik dostarczania jest zwiększany.

| Scenariusz | Czy liczba dostaw komunikatów jest zwiększana |
|----------|---------------------------------------------|
| Sesja została zaakceptowana, ale blokada sesji wygasa (z powodu przekroczenia limitu czasu) | Tak |
| Sesja zostanie zaakceptowana, komunikaty w sesji nie są wykonywane (nawet jeśli są zablokowane), a sesja jest ZAMKNIĘTA | Nie |
| Sesja została zaakceptowana, komunikaty są uzupełniane, a sesja jest jawnie ZAMKNIĘTA | Nie dotyczy (jest to standardowy przepływ. Komunikaty w tym miejscu są usuwane z sesji) |

## <a name="request-response-pattern"></a>Wzorzec żądania-odpowiedzi
[Wzorzec żądanie-odpowiedź](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) to dobrze ustanowiony wzorzec integracji, który umożliwia aplikacji nadawcy wysłanie żądania i pozwala odbiornikowi prawidłowo wysyłać odpowiedź z powrotem do aplikacji nadawcy. Ten wzorzec zwykle wymaga kolejki lub tematu o krótkim czasie, aby aplikacja mogła wysyłać odpowiedzi do programu. W tym scenariuszu sesje zapewniają proste rozwiązanie alternatywne z porównywalną semantyką. 

Wiele aplikacji może wysyłać żądania do pojedynczej kolejki żądań z określonym parametrem nagłówka ustawionym na unikatową identyfikację aplikacji nadawcy. Aplikacja odbiornika może przetwarzać żądania przychodzące z kolejki i wysyłać odpowiedzi w kolejce z włączoną obsługą sesji, ustawiając identyfikator sesji na unikatowy identyfikator, który nadawca przesłał na komunikat żądania. Aplikacja, która wysłała żądanie, może następnie odbierać komunikaty na określonym IDENTYFIKATORze sesji i prawidłowo przetwarzać odpowiedzi.

> [!NOTE]
> Aplikacja, która wysyła początkowe żądania powinna znać identyfikator sesji i używać jej do akceptowania sesji, tak aby sesja, na której oczekuje odpowiedzi, została zablokowana. Dobrym pomysłem jest użycie identyfikatora GUID, który jednoznacznie identyfikuje wystąpienie aplikacji jako identyfikator sesji. Nie powinna istnieć procedura obsługi sesji lub limit czasu określony w odbiorniku sesji dla kolejki, aby upewnić się, że odpowiedzi są dostępne do zablokowania i przetworzenia przez określone odbiorniki.

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy Azure. Messaging. ServiceBus dla platformy .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Azure Service Bus Biblioteka kliencka dla języka Java — przykłady](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus Biblioteka kliencka dla języka Python — przykłady](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus Biblioteka kliencka dla języka JavaScript — przykłady](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus Biblioteka kliencka dla przykładów języka TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Microsoft. Azure. ServiceBus — przykłady dla platformy .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) (sesje i przykłady sessionState)  

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz [Service Bus Queues, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md).

[1]: ./media/message-sessions/sessions.png
