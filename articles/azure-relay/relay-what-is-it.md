---
title: Co to jest usługa Azure Relay? | Microsoft Docs
description: Ten artykuł zapewnia omówienie usługi Azure Relay, która umożliwia opracowywanie aplikacji w chmurze używających usług lokalnych uruchomionych w sieci firmowej, bez otwierania połączenia przez zaporę ani wprowadzania niepożądanych zmian w infrastrukturze sieci.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 0c9a67bcad47ad2e7284bcf6e70914d2ffb06a8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85322648"
---
# <a name="what-is-azure-relay"></a>Co to jest usługa Azure Relay?
Usługa Azure Relay umożliwia bezpieczne uwidacznianie usług uruchomionych w sieci firmowej do chmury publicznej. Możesz to zrobić bez otwierania portu na zaporze lub wprowadzania niepożądanych zmian w firmowej infrastrukturze sieci. 

Usługa przekazywania obsługuje następujące scenariusze pomiędzy usługami lokalnymi i aplikacjami uruchomionymi w chmurze lub w innym środowisku lokalnym. 

- Tradycyjne, jednokierunkowe żądania/odpowiedzi oraz komunikacja równorzędna; 
- dystrybucja zdarzeń w zakresie Internetu w celu obsługi scenariuszów publikowania/subskrybowania; 
- dwukierunkowa i niebuforowana komunikacja przez gniazdo w granicach sieci.

Usługa Azure Relay różni się od technologii integracji na poziomie sieci, takich jak sieci VPN. Usługa Azure Relay może mieć zakres jednego punktu końcowego aplikacji na jednej maszynie. Technologia VPN narzuca więcej zmian, ponieważ polega na modyfikowaniu środowiska sieciowego. 

## <a name="basic-flow"></a>Podstawowy przepływ
We wzorcu transferu danych z przekazywaniem podstawowe kroki są następujące:

1. Usługa lokalna łączy się z usługą przekazywania poprzez port wychodzący. 
2. Tworzy gniazdo dwukierunkowe na potrzeby komunikacji powiązanej z określonym adresem. 
3. Klient następnie może komunikować się z lokalną usługą poprzez wysyłanie ruchu do usługi przekazywania kierującej komunikaty do tego adresu. 
4. Usługa przekazywania następnie *przekazuje* komunikaty do usługi lokalnej za pośrednictwem gniazda dwukierunkowego dedykowanego dla klienta. Klient nie potrzebuje połączenia bezpośredniego z usługą lokalną. Nie musi znać lokalizacji usługi. Ponadto usługa lokalna nie wymaga żadnych otwartych portów przychodzących w zaporze.


## <a name="features"></a>Funkcje 
Usługa Azure Relay ma dwie funkcje:

- [Połączenia hybrydowe](#hybrid-connections) — używają otwartego standardu gniazd sieci Web umożliwiającego scenariusze wieloplatformowe.
- Przekaźniki WCF — używają platformy Windows Communication Foundation (WCF) w celu umożliwienia zdalnego wywoływania procedur. Przekaźnik WCF to starsza wersja funkcji przekazywania, której wielu klientów już używa w modelach programowania platformy WCF.

## <a name="hybrid-connections"></a>Połączenia hybrydowe

Funkcja połączeń hybrydowych w usłudze Azure Relay to bezpieczna, oparta na otwartym protokole ewolucja funkcji usługi Relay, które istniały wcześniej. Możesz użyć jej na dowolnej platformie i w dowolnym języku. Funkcja połączeń hybrydowych w usłudze Azure Relay jest oparta na protokołach HTTP i WebSockets. Takie rozwiązanie umożliwia wysyłanie żądań i odbieranie odpowiedzi za pośrednictwem obiektów Web Socket lub protokołu HTTP(S). Ta funkcja jest zgodna z interfejsem API protokołu WebSocket w popularnych przeglądarkach internetowych. 

Aby uzyskać szczegółowe informacje o protokole połączenia hybrydowego, zobacz [Przewodnik dotyczący protokołu połączeń hybrydowych](relay-hybrid-connections-protocol.md). Możesz użyć połączeń hybrydowych z dowolną biblioteką obiektów Web Socket dla dowolnego środowiska uruchomieniowego/języka.

> [!NOTE]
> Połączenia hybrydowe usługi Azure Relay zastępują starą funkcję połączeń hybrydowych usług BizTalk Services. Funkcja połączeń hybrydowych w usługach BizTalk Services została oparta na funkcji przekaźnika WCF w usłudze Azure Service Bus. Możliwości funkcji połączeń hybrydowych w usłudze Azure Relay uzupełniają już istniejącą funkcję przekaźnika WCF. Te dwie możliwości usług (Przekaźnik WCF oraz Połączenia hybrydowe) istnieją obok siebie w usłudze Azure Relay. Korzystają one ze wspólnej bramy, ale pod innymi względami są to różne implementacje.

## <a name="wcf-relay"></a>Przekaźnik WCF
Przekaźnik WCF działa z pełnym programem .NET Framework i technologią WCF. Należy utworzyć połączenie między usługą lokalną i usługą przekaźnika przy użyciu zestawu powiązań „przekaźników” WCF. Powiązania przekaźników są mapowane na nowe elementy powiązania transportu przeznaczone do tworzenia składników kanału WCF, które integrują się w chmurze z usługą Service Bus. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi WCF Relay](service-bus-relay-tutorial.md).

## <a name="hybrid-connections-vs-wcf-relay"></a>Połączenia hybrydowe a WCF Relay
Zarówno połączenia hybrydowe, jak i przekaźniki WCF umożliwiają bezpieczne połączenie z zasobami istniejącymi w sieci firmowej. Użycie jednej lub drugiej zależy od Twoich konkretnych potrzeb zgodnie z opisem w poniższej tabeli:

|  | Przekaźnik WCF | Połączenia hybrydowe |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **Java script/Node.JS** | |x |
| **Protokół otwarty oparty na standardach** | |x |
| **Modele programowania RPC** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Architektura: przetwarzanie przychodzących żądań przekaźnika
Na poniższym diagramie przedstawiono sposób obsługiwania przychodzących żądań przekazywania przez usługę Azure Relay:

![Przetwarzanie przychodzących żądań przekaźnika WCF](./media/relay-what-is-it/ic690645.png)

1. Klient nasłuchujący wysyła żądanie nasłuchiwania do usługi Azure Relay. Moduł równoważenia obciążenia platformy Azure przekierowuje żądanie do jednego z węzłów bramy. 
2. Usługa Azure Relay tworzy przekaźnik w magazynie bramy. 
3. Klient wysyłający wysyła żądanie nawiązania połączenia z usługą nasłuchiwania. 
4. Brama odbierająca żądanie wyszukuje przekaźnik w magazynie bramy. 
5. Brama przekierowuje żądanie połączenia do odpowiedniej bramy wspomnianej w magazynie bramy. 
6. Brama wysyła żądanie do klienta nasłuchującego tej bramy, aby utworzyć tymczasowy kanał do węzła bramy, który znajduje się najbliżej klienta wysyłającego. 
7. Klient nasłuchujący tworzy tymczasowy kanał w bramie znajdującej się najbliżej klienta wysyłającego. Po nawiązaniu połączenia między klientami za pośrednictwem bramy klienci mogą wymieniać między sobą komunikaty. 
8. Brama przekazuje wszystkie komunikaty od klienta nasłuchiwania do klienta wysyłającego. 
9. Brama przesyła dalej komunikaty z klienta wysyłającego do klienta nasłuchującego.  

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do obiektów WebSocket platformy .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do żądań HTTP platformy .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Wprowadzenie do obiektów WebSocket środowiska Node](relay-hybrid-connections-node-get-started.md)
* [Wprowadzenie do żądań HTTP środowiska Node](relay-hybrid-connections-http-requests-node-get-started.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)

