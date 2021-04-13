---
title: Omówienie AMQP 1,0 w Azure Service Bus
description: Dowiedz się, jak Azure Service Bus obsługuje Advanced Message Queuing Protocol (AMQP), Open standard Protocol.
ms.topic: article
ms.date: 04/08/2021
ms.openlocfilehash: 006511789bfa93f8e7d578ed21a73a2563fb4c6b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304424"
---
# <a name="advanced-message-queueing-protocol-amqp-10-support-in-service-bus"></a>Obsługa protokołu Advanced Message Queueing Protocol (AMQP) 1,0 w Service Bus
Usługa Azure Service Bus w chmurze używa jako podstawowego środka komunikacji [AMQP 1,0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) . Firma Microsoft współpracuje z partnerami w branży, zarówno klientom, jak i dostawcom konkurujących brokerów obsługi komunikatów, aby rozwijać i rozwijać AMQP w ciągu ostatniej dekady, z nowymi rozszerzeniami opracowanymi w ramach [języka Oasis AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp). AMQP 1,0 to standard ISO i IEC ([iso 19464:20149](https://www.iso.org/standard/64955.html)). 

AMQP umożliwia tworzenie aplikacji hybrydowych dla wielu platform przy użyciu neutralnych od dostawcy i niezależnych od implementacji protokołu Open Standard. Aplikacje można konstruować przy użyciu składników utworzonych przy użyciu różnych języków i struktur, które działają w różnych systemach operacyjnych. Wszystkie te składniki mogą łączyć się z Service Bus i bezproblemowo wymieniać strukturalne wiadomości biznesowe oraz z pełną dokładnością.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Wprowadzenie: co to jest AMQP 1,0 i dlaczego jest ważne?
Tradycyjnie, zorientowane na komunikaty oprogramowanie pośredniczące wykorzystuje protokoły własnościowe do komunikacji między aplikacjami klienckimi i brokerami. Oznacza to, że po wybraniu brokera obsługi komunikatów określonego dostawcy należy użyć bibliotek tego dostawcy, aby połączyć aplikacje klienckie z tym brokerem. Wynika to z stopnia zależności od tego dostawcy, ponieważ port aplikacji do innego produktu wymaga wprowadzenia zmian w kodzie we wszystkich połączonych aplikacjach. W społeczności języka Java standardy interfejsu API charakterystyczne dla języka, takie jak usługa wiadomości Java (JMS) i abstrakcje struktury sprężyny, zostały wyeliminowane, ale mają wąski zakres funkcji i nie mogą wykluczać deweloperów przy użyciu innych języków.

Ponadto łączenie brokerów komunikatów z różnych dostawców jest trudne. Zwykle wymaga mostkowania na poziomie aplikacji przenoszenia komunikatów z jednego systemu do drugiego i przetłumaczenia ich własnościowe formaty komunikatów. Jest to typowy wymóg; na przykład, jeśli musisz podać nowy ujednolicony interfejs dla starszych systemów lub zintegrować systemy IT po fuzji. AMQP umożliwia bezpośrednie łączenie brokerów łączących się bezpośrednio, na przykład za pomocą routerów, takich jak [Apache Qpid](https://qpid.apache.org/components/dispatch-router/index.html) lub Broker-native "shovels", podobnie jak w przypadku jednego z [RabbitMQ](service-bus-integrate-with-rabbitmq.md).

Branża oprogramowania to szybko przenoszona firma; nowe języki programowania i struktury aplikacji są wprowadzane w niebewildering tempie. Podobnie wymagania systemu IT są roznoszone w miarę upływu czasu, a deweloperzy chcą korzystać z najnowszych funkcji platformy. Jednak czasami wybrany dostawca obsługi komunikatów nie obsługuje tych platform. Jeśli protokoły obsługi wiadomości są zastrzeżone, nie jest możliwe, aby udostępnić biblioteki dla tych nowych platform. W związku z tym należy użyć podejścia, takiego jak tworzenie bram lub mostków, które umożliwiają dalsze korzystanie z produktu do obsługi komunikatów.

Rozwój Advanced Message Queuing Protocol (AMQP) 1,0 został umotywowany przez te problemy. Pochodzi ona z Morgan Chase, kto, podobnie jak większość firmowych usług finansowych, jest ciężkimi użytkownikami oprogramowania pośredniczącego zorientowanego na komunikaty. Cel był prosty: Aby utworzyć protokół obsługi komunikatów typu Open-Standard, który umożliwia tworzenie aplikacji opartych na komunikatach przy użyciu składników utworzonych przy użyciu różnych języków, platform i systemów operacyjnych, przy użyciu najlepszych składników z wielu dostawców.

## <a name="amqp-10-technical-features"></a>AMQP 1,0 — funkcje techniczne
AMQP 1,0 to wydajny, niezawodny protokół obsługi komunikatów na poziomie sieci, który umożliwia tworzenie niezawodnych aplikacji do obsługi komunikatów na wielu platformach. Protokół ma prosty cel: do definiowania Mechanics bezpiecznych, niezawodnych i wydajnych transferów komunikatów między dwiema stronami. Same wiadomości są kodowane przy użyciu przenośnej reprezentacji danych, która umożliwia niejednorodnym nadawcom i odbiornikom wymianę strukturalnych komunikatów służbowych z pełną dokładnością. Oto podsumowanie najważniejszych funkcji:

* **Wydajne**: AMQP 1,0 to protokół zorientowany na połączenia, który używa kodowania binarnego dla instrukcji protokołu i komunikatów służbowych przesyłanych w tym miejscu. Obejmuje ona zaawansowane schematy kontroli przepływu w celu zmaksymalizowania wykorzystania sieci i podłączonych składników. Wspomniany protokół został zaprojektowany w celu zrównoważenia równowagi między wydajnością, elastycznością i współdziałaniem.
* **Niezawodne**: protokół AMQP 1,0 umożliwia wymianę komunikatów z zakresem gwarancji niezawodności, od pożaru i zapomnienia do niezawodnego, dokładnie po potwierdzeniu dostawy.
* **Elastyczny**: AMQP 1,0 to elastyczny protokół, który może służyć do obsługi różnych topologii. Tego samego protokołu można używać w przypadku komunikacji klient-klient, klient-do-Broker i komunikacja między brokerem a brokerem.
* **Broker-model niezależny**: specyfikacja AMQP 1,0 nie ma żadnych wymagań dotyczących modelu obsługi komunikatów używanego przez brokera. Oznacza to, że można łatwo dodać obsługę AMQP 1,0 do istniejących brokerów obsługi komunikatów.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1,0 jest standardem (z stolicą)
AMQP 1,0 jest międzynarodowym standardem zatwierdzonym przez ISO i IEC jako ISO/IEC 19464:2014.

AMQP 1,0 jest opracowywany od 2008 przez grupę podstawową ponad 20 firm, zarówno dostawców technologii, jak i przedsiębiorstwa użytkowników końcowych. W tym czasie przedsiębiorstwa użytkowników przyczyniły swoje rzeczywiste wymagania biznesowe, a dostawcy technologii rozwijający protokół, aby spełnić te wymagania. W trakcie całego procesu dostawcy uczestniczyły w warsztatach, w których wspólnie współpracują w celu zweryfikowania współdziałania między ich implementacjami.

W październiku 2011 praca rozwojowa przeniesiona do Komitetu Technicznego w organizacji na potrzeby rozwoju standardów informacji o strukturze (języka Oasis) i języka Oasis AMQP 1,0 standard został opublikowany w październiku 2012. Następujące firmy uczestniczyły w Komitecie technicznym podczas opracowywania standardu:

* **Dostawcy technologii**: oprogramowanie axway, technologie Huawei, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, technologie Primeton, oprogramowanie do postępu, Red Hat, sita, oprogramowanie AG, Solace Systems, VMware, WSO2, Zenika.
* **Firmy użytkowników**: Bank of America, kredyty Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan.

Bieżące przewodniczący [komitetu technicznego języka Oasis AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) reprezentują Red Hat i firmę Microsoft.

Niektóre z najczęściej natoczonych korzyści z używania otwartych standardów obejmują:

* Mniejsza szansa blokowania dostawcy
* Współdziałanie
* Szeroka dostępność bibliotek i narzędzi
* Ochrona przed utratą
* Dostępność personelu z możliwością wiedzy
* Mniejsze i zarządzane ryzyko

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 i Service Bus
Obsługa usługi AMQP 1,0 w Azure Service Bus oznacza, że można używać funkcji kolejkowania Service Bus i publikowania/subskrybowania komunikatów obsługiwanych przez brokera z różnych platform przy użyciu wydajnego protokołu binarnego. Ponadto można tworzyć aplikacje składające się ze składników utworzonych przy użyciu różnych języków, struktur i systemów operacyjnych.

Na poniższej ilustracji przedstawiono przykładowe wdrożenie, w którym klienci Java uruchamiani w systemie Linux, zapisanie przy użyciu standardowego interfejsu API usługi wiadomości języka Java (JMS) i klientów platformy .NET działających w systemie Windows, wymieniają komunikaty za pośrednictwem Service Bus przy użyciu AMQP 1,0.

![Diagram przedstawiający jedną Service Bus wymianę komunikatów z dwoma środowiskami Linux i dwoma środowiskami systemu Windows.][0]

**Rysunek 1. przykładowy scenariusz wdrażania przedstawiający obsługę komunikatów na wielu platformach przy użyciu Service Bus i AMQP 1,0**

Wszystkie obsługiwane Service Bus biblioteki klienckie dostępne za pośrednictwem zestawu Azure SDK używają AMQP 1,0.

- [Azure Service Bus dla platformy .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Biblioteki Azure Service Bus dla języka Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Dostawca Azure Service Bus dla języka Java JMS 2,0](how-to-use-java-message-service-20.md)
- [Moduły Azure Service Bus dla języków JavaScript i TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Biblioteki Azure Service Bus dla języka Python](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Ponadto można użyć Service Bus z dowolnego stosu zgodnych protokołów AMQP 1,0:

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>Podsumowanie
* AMQP 1,0 to otwarty, niezawodny protokół do obsługi komunikatów, za pomocą którego można tworzyć aplikacje hybrydowe dla wielu platform. AMQP 1,0 jest standardem języka Oasis.

## <a name="next-steps"></a>Następne kroki
Chcesz dowiedzieć się więcej? Odwiedź następujące linki:

* [Korzystanie z Service Bus z platformy .NET z AMQP]
* [Używanie Service Bus z języka Java z AMQP]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Korzystanie z Service Bus z platformy .NET z AMQP]: service-bus-amqp-dotnet.md
[Używanie Service Bus z języka Java z AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md

