---
title: Izolowanie Azure Service Bus aplikacji przed awariami i katastrofami
description: W tym artykule przedstawiono techniki ochrony aplikacji przed potencjalną Azure Service Bus awarią.
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: b9090a54cd58788dbd13f528af4dda4aa96005b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374596"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Najlepsze rozwiązania dotyczące izolowania aplikacji od wyłączeń i awarii usługi Service Bus

Aplikacje o znaczeniu strategicznym muszą działać w sposób ciągły, nawet w przypadku nieplanowanych przestojów lub awarii. W tym artykule opisano techniki, których można użyć do ochrony Service Bus aplikacji przed potencjalną awarią lub awarią usługi.

Awaria jest definiowana jako tymczasowa niedostępność Azure Service Bus. Awaria może mieć wpływ na niektóre składniki Service Bus, takie jak magazyn komunikatów, a nawet całe centrum danych. Po naprawieniu problemu Service Bus znów dostępna. Zazwyczaj awaria nie powoduje utraty komunikatów ani innych danych. Przykładem awarii składnika jest niedostępność określonego magazynu komunikatów. Przykładem przestoju w całym centrum danych jest awaria zasilacza centrum Awaria może trwać od kilku minut do kilku dni.

Awaria jest definiowana jako stała utrata Service Bus jednostki skalowania lub centrum danych. Centrum danych może lub nie staje się ponownie dostępne. Zazwyczaj awaria powoduje utratę niektórych lub wszystkich komunikatów lub innych danych. Przykładami awarii są pożary, powodzi lub ziemi.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Ochrona przed awariami i katastrofami — Service Bus Premium
Pojęcia dotyczące wysokiej dostępności i odzyskiwania po awarii są wbudowane bezpośrednio w warstwę Azure Service Bus Premium, zarówno w tym samym regionie (za pośrednictwem Strefy dostępności), jak i w różnych regionach (za pośrednictwem Geo-Disaster Recovery).

### <a name="geo-disaster-recovery"></a>Geo-Disaster odzyskiwanie

Service Bus Premium obsługuje odzyskiwanie geograficzne po awarii na poziomie przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [Azure Service Bus geograficznie z odzyskiwaniem po awarii](service-bus-geo-dr.md). Funkcja odzyskiwania po awarii dostępna tylko dla [jednostki SKU Premium](service-bus-premium-messaging.md) , implementuje odzyskiwanie po awarii metadanych i opiera się na podstawowych i pomocniczych przestrzeniach nazw odzyskiwania po awarii. W przypadku odzyskiwania Geo-Disaster tylko metadane dla jednostek są replikowane między podstawowymi i pomocniczymi przestrzeniami nazw.  

### <a name="availability-zones"></a>Strefy dostępności

Jednostka SKU Service Bus Premium obsługuje [strefy dostępności](../availability-zones/az-overview.md), zapewniając izolowane lokalizacje w ramach tego samego regionu świadczenia usługi Azure. Service Bus zarządza trzema kopiami magazynu komunikatów (1 Podstawowa i 2). Service Bus synchronizuje wszystkie trzy kopie na potrzeby operacji związanych z danymi i zarządzaniem. Jeśli podstawowa kopia nie powiedzie się, jedna z kopii pomocniczych jest podwyższana do podstawowej bez postrzeganego przestoju. Jeśli aplikacje Zobacz przejściowe odłączenia od Service Bus, logika ponowień w zestawie SDK automatycznie ponownie nawiąże połączenie z Service Bus. 

W przypadku korzystania ze stref dostępności zarówno metadane, jak i dane (komunikaty) są replikowane między centrami danych w strefie dostępności. 

> [!NOTE]
> Strefy dostępności wsparcia dla Azure Service Bus Premium jest dostępna tylko w [regionach świadczenia usługi Azure](../availability-zones/az-region.md) , w których znajdują się strefy dostępności.

Strefy dostępności można włączyć tylko dla nowych przestrzeni nazw, korzystając z Azure Portal. Service Bus nie obsługuje migracji istniejących przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po włączeniu jej w przestrzeni nazw.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Ochrona przed awariami i katastrofami — Service Bus Standard
Aby zapewnić odporność na awarie centrum danych w przypadku korzystania ze standardowej warstwy cenowej wiadomości, Service Bus obsługuje dwie podejścia: *aktywne* i *pasywne* replikacja. Dla każdego podejścia, jeśli dana Kolejka lub temat muszą pozostać dostępne w obecności centrum danych, można utworzyć je w obu obszarach nazw. Obie jednostki mogą mieć taką samą nazwę. Na przykład Kolejka główna może zostać osiągnięta w obszarze **contosoPrimary.ServiceBus.Windows.NET/myQueue**, podczas gdy jej pomocniczy odpowiednik można osiągnąć w obszarze **contosoSecondary.ServiceBus.Windows.NET/myQueue**.

>[!NOTE]
> **Replikacja aktywna** i konfiguracja **replikacji pasywnej** są rozwiązaniami ogólnego przeznaczenia i nie konkretnymi funkcjami Service Bus. Logika replikacji (wysyłająca do 2 różnych przestrzeni nazw) znajduje się w aplikacjach nadawcy, a odbiorca musi mieć logikę niestandardową na potrzeby wykrywania duplikatów.

Jeśli aplikacja nie wymaga stałego komunikacji nadawcy z odbiornikiem, aplikacja może zaimplementować trwałą kolejkę po stronie klienta, aby zapobiec utracie komunikatów i zabezpieczyć nadawcę przed wszelkimi przejściowymi błędami Service Bus.

### <a name="active-replication"></a>Aktywna replikacja
Aktywna replikacja używa jednostek w obu obszarach nazw dla każdej operacji. Każdy klient, który wysyła komunikat, wysyła do niej dwie kopie tego samego komunikatu. Pierwsza kopia jest wysyłana do jednostki podstawowej (na przykład **contosoPrimary.ServiceBus.Windows.NET/Sales**), a druga kopia komunikatu jest wysyłana do jednostki pomocniczej (na przykład **contosoSecondary.ServiceBus.Windows.NET/Sales**).

Klient odbiera komunikaty z obu kolejek. Odbiornik przetwarza pierwszą kopię wiadomości, a druga kopia jest pomijana. Aby pominąć duplikowanie komunikatów, nadawca musi oznaczyć każdy komunikat unikatowym identyfikatorem. Obie kopie wiadomości muszą być oznaczone tym samym identyfikatorem. Aby oznaczyć komunikat, można użyć właściwości [BrokeredMessage. MessageId][BrokeredMessage.MessageId] lub [BrokeredMessage. Label][BrokeredMessage.Label] lub właściwości niestandardowej. Odbiornik musi obsługiwać listę komunikatów, które zostały już odebrane.

Przykład [replikacji geograficznej z Service Bus warstwy standardowej][Geo-replication with Service Bus Standard Tier] ilustruje aktywną replikację jednostek obsługi komunikatów.

> [!NOTE]
> Podejście aktywnej replikacji podwaja liczbę operacji, dlatego takie podejście może prowadzić do wyższych kosztów.
> 
> 

### <a name="passive-replication"></a>Replikacja pasywna
W przypadku niewolnej awarii replikacja pasywna używa tylko jednej z dwóch jednostek obsługi komunikatów. Klient wysyła komunikat do aktywnej jednostki. Jeśli operacja na aktywnej jednostce kończy się niepowodzeniem z kodem błędu, który wskazuje, że centrum danych, które obsługuje aktywną jednostkę, może być niedostępne, klient wysyła kopię wiadomości do jednostki kopii zapasowej. W tym momencie jednostki aktywne i zapasowe przełączają role: klient wysyłający traktuje starą aktywną jednostkę jako nową jednostkę kopii zapasowej, a stara jednostka kopii zapasowej jest nową aktywną jednostką. Jeśli oba operacje wysyłania zakończą się niepowodzeniem, role dwóch jednostek pozostaną niezmienione i zostanie zwrócony błąd.

Klient odbiera komunikaty z obu kolejek. Ze względu na to, że odbiornik odbiera dwie kopie tego samego komunikatu, odbiorca musi pominąć zduplikowane komunikaty. Duplikaty można pominąć w taki sam sposób, jak opisano w przypadku aktywnej replikacji.

Ogólnie rzecz biorąc replikacja pasywna jest większa od aktywnej replikacji, ponieważ w większości przypadków wykonywana jest tylko jedna operacja. Opóźnienie, przepływność i koszt pieniężny są identyczne z niereplikowanym scenariuszem.

W przypadku korzystania z replikacji pasywnej komunikaty mogą być tracone lub odbierane dwa razy:

* **Opóźnienie komunikatów lub utrata**: Załóżmy, że nadawca pomyślnie wysłał komunikat M1 do kolejki głównej, a następnie Kolejka staje się niedostępna, zanim odbiornik otrzyma M1. Nadawca wysyła kolejny komunikat m2 do kolejki pomocniczej. Jeśli kolejka podstawowa jest tymczasowo niedostępna, odbiornik otrzymuje M1 po ponownym udostępnieniu kolejki. W przypadku awarii odbiorca może nigdy nie otrzymać M1.
* **Odbiór duplikatu**: Załóżmy, że nadawca wysyła komunikat m do kolejki podstawowej. Service Bus pomyślnie przetwarzał m, ale nie może wysłać odpowiedzi. Po upływie limitu czasu dla operacji wysyłania nadawca wysyła identyczną kopię m do kolejki pomocniczej. Jeśli odbiornik jest w stanie odebrać pierwszą kopię m, zanim kolejka podstawowa stanie się niedostępna, odbiorca odbiera obie kopie m w tym samym czasie. Jeśli odbiorca nie może odebrać pierwszej kopii m, zanim kolejka podstawowa stanie się niedostępna, odbiorca początkowo otrzymuje tylko drugą kopię m, a następnie otrzyma drugą kopię m, gdy kolejka podstawowa stanie się dostępna.

Przykład [replikacji geograficznej z Service Bus warstwy standardowej][Geo-replication with Service Bus Standard Tier] ilustruje pasywną replikację jednostek obsługi komunikatów.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Ochrona punktów końcowych przekaźnika przed awariami centrum danych i awariami
Replikacja geograficzna dla punktów końcowych [Azure Relay](../azure-relay/relay-what-is-it.md) umożliwia usłudze, która uwidacznia punkt końcowy usługi Relay w obecności Service Bus awarii. Aby osiągnąć replikację geograficzną, usługa musi utworzyć dwa punkty końcowe przekaźnika w różnych przestrzeniach nazw. Przestrzenie nazw muszą znajdować się w różnych centrach danych, a dwa punkty końcowe muszą mieć różne nazwy. Na przykład można uzyskać dostęp do podstawowego punktu końcowego w obszarze **contosoPrimary.ServiceBus.Windows.NET/myPrimaryService**, podczas gdy jego pomocniczy odpowiednik można uzyskać w obszarze **contosoSecondary.ServiceBus.Windows.NET/mySecondaryService**.

Usługa następnie nasłuchuje na obu punktach końcowych, a klient może wywołać usługę za pośrednictwem jednego z punktów końcowych. Aplikacja kliencka losowo wybiera jedno z przekaźników jako podstawowy punkt końcowy i wysyła żądanie do aktywnego punktu końcowego. Jeśli operacja kończy się niepowodzeniem z kodem błędu, ten błąd wskazuje, że punkt końcowy przekazywania nie jest dostępny. Aplikacja otwiera kanał do punktu końcowego kopii zapasowej i ponownie wystawia żądanie. W tym momencie aktywne i punkty końcowe kopii zapasowej przełączają role: aplikacja kliencka traktuje stary aktywny punkt końcowy jako nowy punkt końcowy kopii zapasowej, a stary punkt końcowy kopii zapasowej będzie nowym aktywnym punktem końcowym. Jeśli oba operacje wysyłania zakończą się niepowodzeniem, role dwóch jednostek pozostaną niezmienione i zostanie zwrócony błąd.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat odzyskiwania po awarii, zobacz następujące artykuły:

* [Azure Service Bus geograficznie — odzyskiwanie po awarii](service-bus-geo-dr.md)
* [Azure SQL Database ciągłość działania firmy][Azure SQL Database Business Continuity]
* [Projektowanie aplikacji odpornych na błędy dla platformy Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]:../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png