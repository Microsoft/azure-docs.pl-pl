---
title: Azure Service Bus — przeglądanie komunikatów
description: Przeglądanie i Service Bus komunikatów umożliwia klientowi Azure Service Bus wyliczanie wszystkich komunikatów w kolejce lub subskrypcji.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: deafe9e6ddeeebf233922aade36823ddaaede864
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520126"
---
# <a name="message-browsing"></a>Przeglądanie komunikatów
Przeglądanie lub podgląd komunikatów umożliwia klientowi Service Bus wyliczanie wszystkich komunikatów w kolejce lub subskrypcji na potrzeby diagnostyki i debugowania.

Operacja Podgląd kolejki lub subskrypcji zwraca najwyżej żądaną liczbę komunikatów. W poniższej tabeli przedstawiono typy komunikatów zwracanych przez operację Podgląd. 

| Typ komunikatów | Zawarte? | 
| ---------------- | ----- | 
| Aktywne komunikaty | Tak |
| Komunikaty utraconych wiadomości | Nie | 
| Zablokowane komunikaty | Tak |
| Wygasłe komunikaty |  Może być (przed utraconych) |
| Zaplanowane komunikaty | Tak dla kolejek. Nie dla subskrypcji |

## <a name="dead-lettered-messages"></a>Komunikaty utraconych wiadomości
Aby zaglądać do komunikatów utraconych w kolejce lub subskrypcji, należy uruchomić operację podglądu w kolejce utraconych wiadomości skojarzonych z kolejką lub subskrypcją.  Aby uzyskać więcej informacji, zobacz accessing dead letter queues (Uzyskiwanie [dostępu do utraconych kolejek).](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue)

## <a name="expired-messages"></a>Wygasłe komunikaty
Wygasłe komunikaty mogą być uwzględniane w wynikach zwróconych z operacji Podgląd. Zużyte i wygasłe komunikaty są czyszczone przez asynchroniczne uruchamianie "wyrzucania elementów bezużytecznych". Ten krok niekoniecznie występuje natychmiast po wygaśnięciu komunikatów. Dlatego operacja podglądu może zwrócić komunikaty, które już wygasły. Te komunikaty zostaną usunięte lub utracone po następnym wywołaniu operacji odbierania w kolejce lub subskrypcji. Należy pamiętać o tym zachowaniu podczas próby odzyskania odroczonych komunikatów z kolejki. 

Wygasły komunikat nie kwalifikuje się już do regularnego pobierania w jakikolwiek inny sposób, nawet jeśli jest zwracany przez podejrzanie. Zwracanie tych komunikatów jest celowe, ponieważ narzędzie Peek to narzędzie diagnostyczne odzwierciedlające bieżący stan dziennika.

## <a name="locked-messages"></a>Zablokowane komunikaty
Funkcja Podgląd zwraca również komunikaty, które **zostały zablokowane** i są obecnie przetwarzane przez inne odbiorniki. Jednak ze względu na to, że funkcja Peek zwraca rozłączona migawkę, nie można zaobserwować stanu blokady komunikatu w przypadku wyświetlonych komunikatów.

## <a name="peek-apis"></a>Podgląd interfejsów API
Wgląd w kolejki, subskrypcje i kolejki utraconych komunikatów. 

W przypadku wielokrotnego wywoływania operacja podglądu wylicza wszystkie komunikaty w kolejce lub subskrypcji w kolejności od najniższego dostępnego numeru sekwencji do najwyższego. Jest to kolejność, w której komunikaty były w kolejkach, a nie kolejność, w jakiej komunikaty mogą być ostatecznie pobierane.

Można również przekazać numer_sekwencji do operacji wglądu. Zostanie on użyty do określenia, od czego zacząć podejrzanie. Możesz wykonać kolejne wywołania operacji podglądu bez określania parametru w celu dalszego wyliczenia.

## <a name="next-steps"></a>Następne kroki
Wypróbuj przykłady w wybranego języku, aby poznać funkcję podglądu lub przeglądania wiadomości:

- [Azure Service Bus biblioteki klienta dla języka Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)  -  **Przykładowy podgląd komunikatu**
- [Azure Service Bus biblioteki klienta dla języka Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)  -  **receive_peek.py**
- [Azure Service Bus biblioteki klienta dla języka JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  -  **browseMessages.jsprzykład**
- [Azure Service Bus biblioteki klienta dla języka TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)  -  **przykład browseMessages.ts**
- [Przykłady azure.Messaging.ServiceBus dla platformy .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) — zobacz zobacz metody wglądu w klasy odbiorników w [dokumentacji referencyjnej](/dotnet/api/azure.messaging.servicebus).

Poniżej znajdują się przykłady dla starszych bibliotek klienckich .NET i Java:
- [Microsoft.Azure.ServiceBus samples for .NET (Przykłady microsoft.Azure.ServiceBus dla platformy .NET)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)  -  **Przykład przeglądania komunikatów (podgląd)** 
- [przykłady azure-servicebus dla języka Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)  -  **Przykład przeglądania** komunikatów. 
