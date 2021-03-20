---
title: Azure Service Bus — odroczenie komunikatu
description: W tym artykule wyjaśniono, jak odroczyć dostarczanie komunikatów Azure Service Bus. Komunikat pozostanie w kolejce lub subskrypcji, ale zostanie odłożony.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: e3a940f8aa9e72d9b09e9c0a3305521c6f17dfb0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622049"
---
# <a name="message-deferral"></a>Odraczanie komunikatów

Gdy klient z kolejką lub subskrypcją otrzymuje komunikat, który chce przetworzyć, ale w przypadku którego przetwarzanie nie jest obecnie możliwe z powodu specjalnych okoliczności w aplikacji, ma możliwość "odroczone" Pobieranie komunikatu do późniejszego punktu. Komunikat pozostanie w kolejce lub subskrypcji, ale zostanie odłożony.

Odroczenie to funkcja, która została utworzona w scenariuszach przetwarzania przepływu pracy. Struktury przepływu pracy mogą wymagać przetworzenia niektórych operacji w określonej kolejności i może być konieczne odłożenie przetwarzania niektórych odebranych komunikatów do momentu, w którym zostanie wykonane wcześniejsze prace, które zostały poinformowane przez inne komunikaty.

Prosty przykład ilustruje sekwencję przetwarzania zamówień, w której powiadomienie o płatności od zewnętrznego dostawcy płatności pojawia się w systemie, zanim zgodne zamówienie zakupu zostało rozpropagowane ze sklepu od przodu do systemu realizacji. W takim przypadku system realizacji może odroczyć przetwarzanie powiadomienia o płatności do momentu, w którym ma zostać skojarzona kolejność. W przypadku scenariuszy, w których komunikaty z różnych źródeł kierują przepływ pracy do przodu, kolejność wykonywania w czasie rzeczywistym może być poprawna, ale komunikaty odzwierciedlające wyniki mogą być nieuporządkowane.

Ostatecznie w przypadku zmiany kolejności komunikatów z zamówienia przyjęcia do kolejności, w której można je przetworzyć, pozostawiając te komunikaty bezpiecznie w magazynie komunikatów, dla którego przetwarzanie musi zostać odroczone.

> [!NOTE]
> Komunikaty odroczone nie będą automatycznie przenoszone do kolejki utraconych wiadomości [po ich wygaśnięciu](./service-bus-dead-letter-queues.md#exceeding-timetolive). To zachowanie jest zaprojektowane.

## <a name="message-deferral-apis"></a>Interfejsy API odroczeń komunikatów

Interfejs API to [BrokeredMessage. Ustąp](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) lub [BrokeredMessage. DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) w kliencie .NET Framework, [MessageReceiver. DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) w kliencie .NET Standard, a [IMessageReceiver. Ustąp](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer) lub [IMessageReceiver. DeferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync) w kliencie Java. 

Komunikaty odroczone pozostają w kolejce głównej wraz ze wszystkimi innymi aktywnymi komunikatami (w przeciwieństwie do wiadomości utraconych w kolejce), ale nie mogą być już odbierane przy użyciu zwykłych funkcji Receive/ReceiveAsync. Komunikaty odroczone mogą zostać odnalezione za pośrednictwem [przeglądania komunikatów](message-browsing.md) , jeśli aplikacja utraci ich śledzenie.

Aby pobrać odroczony komunikat, jego właściciel jest odpowiedzialny za zapamiętanie [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) . Każdy odbiornik, który zna numer sekwencyjny komunikatu, może później odebrać komunikat jawnie za pomocą `Receive(sequenceNumber)` .

Jeśli nie można przetworzyć komunikatu, ponieważ określony zasób obsługujący ten komunikat jest tymczasowo niedostępny, ale przetwarzanie komunikatów nie powinno być podsumowujące w sposób skrócony, sposób umieszczania tego komunikatu po stronie przez kilka minut to zapamiętanie **SequenceNumber** w [zaplanowanym komunikacie](message-sequencing.md) do opublikowania w ciągu kilku minut i ponowne pobranie odroczonego komunikatu po nadejściu zaplanowanego komunikatu. Jeśli program obsługi komunikatów zależy od bazy danych dla wszystkich operacji, a ta baza danych jest tymczasowo niedostępna, nie powinna używać odroczenia, ale raczej wstrzymuje wysyłanie komunikatów do momentu, gdy baza danych będzie ponownie dostępna.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
