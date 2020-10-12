---
title: Omówienie AMQP 1,0 w Azure Service Bus
description: Dowiedz się, jak Azure Service Bus obsługuje Advanced Message Queuing Protocol (AMQP), Open standard Protocol.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: c91c7965b94216f3f3bcb47e0cb652ce22a0217a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066342"
---
# <a name="amqp-10-support-in-service-bus"></a>Obsługa AMQP 1,0 w Service Bus
Zarówno usługa Azure Service Bus w chmurze, jak i lokalne [Service Bus dla systemu Windows Server (Service Bus 1,1)](/previous-versions/service-bus-archive/dn282144(v=azure.100)) obsługują protokół Advanced Message Queueing Protocol (AMQP) 1,0. AMQP umożliwia tworzenie aplikacji hybrydowych dla wielu platform przy użyciu protokołu Open Standard. Aplikacje można konstruować przy użyciu składników utworzonych przy użyciu różnych języków i struktur, które działają w różnych systemach operacyjnych. Wszystkie te składniki mogą łączyć się z Service Bus i bezproblemowo wymieniać strukturalne wiadomości biznesowe oraz z pełną dokładnością.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Wprowadzenie: co to jest AMQP 1,0 i dlaczego jest ważne?
Tradycyjnie, zorientowane na komunikaty oprogramowanie pośredniczące wykorzystuje protokoły własnościowe do komunikacji między aplikacjami klienckimi i brokerami. Oznacza to, że po wybraniu brokera obsługi komunikatów określonego dostawcy należy użyć bibliotek tego dostawcy, aby połączyć aplikacje klienckie z tym brokerem. Wynika to z stopnia zależności od tego dostawcy, ponieważ port aplikacji do innego produktu wymaga wprowadzenia zmian w kodzie we wszystkich połączonych aplikacjach. 

Ponadto łączenie brokerów komunikatów z różnych dostawców jest trudne. Zwykle wymaga to mostkowania na poziomie aplikacji przenoszenia komunikatów z jednego systemu do drugiego i przetłumaczenia ich własnościowe formaty komunikatów. Jest to typowy wymóg; na przykład, jeśli musisz podać nowy ujednolicony interfejs dla starszych systemów lub zintegrować systemy IT po fuzji.

Branża oprogramowania to szybko przenoszona firma; nowe języki programowania i struktury aplikacji są wprowadzane w niebewildering tempie. Podobnie wymagania systemu IT są roznoszone w miarę upływu czasu, a deweloperzy chcą korzystać z najnowszych funkcji platformy. Jednak czasami wybrany dostawca obsługi komunikatów nie obsługuje tych platform. Ponieważ protokoły obsługi wiadomości są zastrzeżone, nie jest możliwe, aby udostępnić biblioteki dla tych nowych platform. W związku z tym należy użyć podejścia, takiego jak tworzenie bram lub mostków, które umożliwiają dalsze korzystanie z produktu do obsługi komunikatów.

Rozwój Advanced Message Queuing Protocol (AMQP) 1,0 został umotywowany przez te problemy. Pochodzi ona z Morgan Chase, kto, podobnie jak większość firmowych usług finansowych, jest ciężkimi użytkownikami oprogramowania pośredniczącego zorientowanego na komunikaty. Cel był prosty: Aby utworzyć protokół obsługi komunikatów typu Open-Standard, który umożliwia tworzenie aplikacji opartych na komunikatach przy użyciu składników utworzonych przy użyciu różnych języków, platform i systemów operacyjnych, przy użyciu najlepszych składników z wielu dostawców.

## <a name="amqp-10-technical-features"></a>AMQP 1,0 — funkcje techniczne
AMQP 1,0 to wydajny, niezawodny protokół obsługi komunikatów na poziomie sieci, który umożliwia tworzenie niezawodnych aplikacji do obsługi komunikatów na wielu platformach. Protokół ma prosty cel: do definiowania Mechanics bezpiecznych, niezawodnych i wydajnych transferów komunikatów między dwiema stronami. Same wiadomości są kodowane przy użyciu przenośnej reprezentacji danych, która umożliwia niejednorodnym nadawcom i odbiornikom wymianę strukturalnych komunikatów służbowych z pełną dokładnością. Poniżej znajduje się podsumowanie najważniejszych funkcji:

* **Wydajne**: AMQP 1,0 to protokół zorientowany na połączenia, który używa kodowania binarnego dla instrukcji protokołu i komunikatów służbowych przesyłanych w tym miejscu. Obejmuje ona zaawansowane schematy kontroli przepływu w celu zmaksymalizowania wykorzystania sieci i podłączonych składników. Wspomniany protokół został zaprojektowany w celu zrównoważenia równowagi między wydajnością, elastycznością i współdziałaniem.
* **Niezawodne**: protokół AMQP 1,0 umożliwia wymianę komunikatów z różnymi gwarancjami niezawodności, od pożaru i zapomnień do niezawodnego, dokładnie po potwierdzeniem dostawy.
* **Elastyczny**: AMQP 1,0 to elastyczny protokół, który może służyć do obsługi różnych topologii. Tego samego protokołu można używać w przypadku komunikacji klient-klient, klient-do-Broker i komunikacja między brokerem a brokerem.
* **Broker-model niezależny**: specyfikacja AMQP 1,0 nie wykonuje żadnych wymagań dotyczących modelu obsługi komunikatów używanego przez brokera. Oznacza to, że można łatwo dodać obsługę AMQP 1,0 do istniejących brokerów obsługi komunikatów.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1,0 jest standardem (z stolicą)
AMQP 1,0 jest międzynarodowym standardem zatwierdzonym przez ISO i IEC jako ISO/IEC 19464:2014.

AMQP 1,0 jest opracowywany od 2008 przez grupę podstawową ponad 20 firm, zarówno dostawców technologii, jak i przedsiębiorstwa użytkowników końcowych. W tym czasie przedsiębiorstwa użytkowników przyczyniły swoje rzeczywiste wymagania biznesowe, a dostawcy technologii rozwijający protokół, aby spełnić te wymagania. W trakcie całego procesu dostawcy uczestniczyły w warsztatach, w których wspólnie współpracują w celu zweryfikowania współdziałania między ich implementacjami.

W październiku 2011 praca rozwojowa przeniesiona do Komitetu Technicznego w organizacji na potrzeby rozwoju standardów informacji o strukturze (języka Oasis) i języka Oasis AMQP 1,0 standard został opublikowany w październiku 2012. Następujące firmy uczestniczyły w Komitecie technicznym podczas opracowywania standardu:

* **Dostawcy technologii**: oprogramowanie axway, technologie Huawei, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, technologie Primeton, oprogramowanie do postępu, Red Hat, sita, oprogramowanie AG, Solace Systems, VMware, WSO2, Zenika.
* **Firmy użytkowników**: Bank of America, kredyty Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan.

Niektóre z najczęściej natoczonych korzyści z używania otwartych standardów obejmują:

* Mniejsza szansa blokowania dostawcy
* Współdziałanie
* Szeroka dostępność bibliotek i narzędzi
* Ochrona przed utratą
* Dostępność personelu z możliwością wiedzy
* Mniejsze i zarządzane ryzyko

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 i Service Bus
Obsługa usługi AMQP 1,0 w Azure Service Bus oznacza, że teraz można korzystać z funkcji kolejkowania Service Bus i publikowania/subskrybowania usług komunikatów obsługiwanych przez brokera z różnych platform przy użyciu wydajnego protokołu binarnego. Ponadto można tworzyć aplikacje składające się ze składników utworzonych przy użyciu różnych języków, struktur i systemów operacyjnych.

Na poniższej ilustracji przedstawiono przykładowe wdrożenie, w którym klienci Java uruchamiani w systemie Linux, zapisanie przy użyciu standardowego interfejsu API usługi wiadomości języka Java (JMS) i klientów platformy .NET działających w systemie Windows, wymieniają komunikaty za pośrednictwem Service Bus przy użyciu AMQP 1,0.

![Diagram przedstawiający jedną Service Bus wymianę komunikatów z dwoma środowiskami Linux i dwoma środowiskami systemu Windows.][0]

**Rysunek 1. przykładowy scenariusz wdrażania przedstawiający obsługę komunikatów na wielu platformach przy użyciu Service Bus i AMQP 1,0**

W tej chwili następujące biblioteki klienckie są znane do pracy z Service Bus:

| Język | Biblioteka |
| --- | --- |
| Java |Klient usługi wiadomości w języku Java Apache Qpid (JMS)<br/>IIT Software SwiftMQ Client Java |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton — PHP |
| Python |Proton-Python Apache Qpid |
| C# |AMQP .NET Lite |

**Rysunek 2. tabela bibliotek klienckich AMQP 1,0**

## <a name="summary"></a>Podsumowanie
* AMQP 1,0 to otwarty, niezawodny protokół do obsługi komunikatów, za pomocą którego można tworzyć aplikacje hybrydowe dla wielu platform. AMQP 1,0 jest standardem języka Oasis.
* Obsługa usługi AMQP 1,0 jest teraz dostępna w Azure Service Bus i Service Bus dla systemu Windows Server (Service Bus 1,1). Ceny są takie same, jak w przypadku istniejących protokołów.

## <a name="next-steps"></a>Następne kroki
Chcesz dowiedzieć się więcej? Odwiedź następujące linki:

* [Korzystanie z Service Bus z platformy .NET z AMQP]
* [Używanie Service Bus z języka Java z AMQP]
* [Instalowanie oprogramowania Apache Qpid Proton-C na maszynie wirtualnej z systemem Linux na platformie Azure]
* [AMQP w Service Bus dla systemu Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Korzystanie z Service Bus z platformy .NET z AMQP]: service-bus-amqp-dotnet.md
[Używanie Service Bus z języka Java z AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md
[Instalowanie oprogramowania Apache Qpid Proton-C na maszynie wirtualnej z systemem Linux na platformie Azure]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)