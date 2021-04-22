---
title: Azure Service Bus — odroczenie komunikatów
description: W tym artykule wyjaśniono, jak odroczyć dostarczanie Azure Service Bus wiadomości. Komunikat pozostaje w kolejce lub subskrypcji, ale jest odkładany.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 171fc6e1a3c779802747d34ac631d265e8c8926f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869495"
---
# <a name="message-deferral"></a>Odraczanie komunikatów
Gdy klient kolejki lub subskrypcji otrzymuje komunikat, że chce go przetworzyć, ale przetwarzanie nie jest obecnie możliwe ze względu na specjalne okoliczności, ma możliwość "odroczenia" pobrania komunikatu do późniejszego punktu. Komunikat pozostaje w kolejce lub subskrypcji, ale jest odkładany.

> [!NOTE]
> Odroczone komunikaty nie zostaną automatycznie przeniesione do kolejki utraconych wiadomości [po ich wygaśnięciu.](./service-bus-dead-letter-queues.md#time-to-live) To zachowanie jest zachowaniem projektu.

## <a name="sample-scenarios"></a>Przykładowe scenariusze
Odroczenie to funkcja utworzona specjalnie dla scenariuszy przetwarzania przepływu pracy. Struktury przepływów pracy mogą wymagać przetworzenia niektórych operacji w określonej kolejności. Może być wymagane odłożenie przetwarzania niektórych odebranych komunikatów do czasu ukończenia określonych wcześniej prac, które zostały poinformowane przez inne komunikaty.

Prostym ilustratywnym przykładem jest sekwencja przetwarzania zamówień, w której w systemie pojawia się powiadomienie o płatności od zewnętrznego dostawcy płatności, zanim pasujące zamówienie zakupu zostanie rozpropagowane z frontu sklepu do systemu realizacji. W takim przypadku system realizacji może odroczyć przetwarzanie powiadomienia o płatności do momentu, gdy istnieje zamówienie, z którym je skojarzyć. W scenariuszach, w których komunikaty z różnych źródeł przepływu pracy są przekazywane dalej, kolejność wykonywania w czasie rzeczywistym może być poprawna, ale komunikaty odzwierciedlające wyniki mogą być odbierane w innej kolejności.

Ostatecznie odroczenie pomaga w zmienianiu kolejności komunikatów z zamówienia przylotu na kolejność, w której mogą być przetwarzane, pozostawiając te komunikaty bezpiecznie w magazynie komunikatów, dla którego należy odłożyć przetwarzanie.

Jeśli nie można przetworzyć komunikatu, ponieważ określony zasób do obsługi tego komunikatu jest tymczasowo niedostępny, ale przetwarzanie komunikatów nie powinno być tymczasowo wstrzymane, [](message-sequencing.md) sposobem na umieść ten komunikat po kilku minutach jest zapamiętanie numeru sekwencji w zaplanowanym komunikacie, który ma zostać opublikowany w ciągu kilku minut, i ponowne pobranie odroczonego komunikatu po przyjściu zaplanowanego komunikatu. Jeśli procedura obsługi komunikatów zależy od bazy danych dla wszystkich operacji i ta baza danych jest tymczasowo niedostępna, nie powinna używać funkcji odroczenia, lecz całkowicie wstrzymać odbieranie komunikatów do momentu, gdy baza danych będzie ponownie dostępna. 

## <a name="retrieving-deferred-messages"></a>Pobieranie komunikatów odroczonych
Komunikaty odroczone pozostają w kolejce głównej wraz ze wszystkimi innymi aktywnymi komunikatami (w przeciwieństwie do komunikatów utraconych, które są aktywne w podkatawisie), ale nie można ich już odbierać za pomocą regularnych operacji odbierania. Odroczone komunikaty można wykryć za pośrednictwem [przeglądania komunikatów,](message-browsing.md) jeśli aplikacja utraci ich śledzenie.

Aby pobrać komunikat odroczony, jego właściciel jest odpowiedzialny za zapamiętanie numeru sekwencji podczas jego odraczania. Każdy odbiornik, który zna numer sekwencji odroczonego komunikatu, może później odbierać komunikat przy użyciu metod odbierania, które odbierają numer sekwencji jako parametr. Aby uzyskać więcej informacji na temat numerów sekwencji, zobacz [Sekwencjonowanie](message-sequencing.md)komunikatów i znaczniki czasu .

## <a name="next-steps"></a>Następne kroki
Wypróbuj przykłady w swoim języku, aby eksplorować Azure Service Bus funkcji. 

- [Azure Service Bus biblioteki klienta dla języka Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus biblioteki klienta dla języka Python](/samples/azure/azure-sdk-for-python/servicebus-samples/) — zobacz przykład **receive_deferred_message_queue.py.** 
- [Azure Service Bus biblioteki klienta dla języka JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/) — zobacz **zaawansowany/deferral.js** przykład. 
- [Azure Service Bus biblioteki klienta dla języka TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/) — zobacz przykład **advanced/deferral.ts.** 
- [Przykłady dla platformy .NET Azure.Messaging.ServiceBus](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) — zobacz **przykład Settling Messages** (Konfigurowanie komunikatów). 

Poniżej znajdują się przykłady dla starszych bibliotek klienta .NET i Java:
- [Przykłady microsoft.Azure.ServiceBus dla platformy .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) — zobacz przykład **odroczenia.** 
- [przykłady azure-servicebus dla języka Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
