---
title: Architektura sieci v1
description: Omówienie architektury topologii sieci w środowiskach App Service. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701817"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Omówienie architektury sieci App Service środowisk
Środowiska App Service są zawsze tworzone w ramach podsieci [sieci wirtualnej][virtualnetwork] — aplikacje działające w App Service Environment mogą komunikować się z prywatnymi punktami końcowymi znajdującymi się w tej samej topologii sieci wirtualnej.  Ponieważ klienci mogą blokować części swojej infrastruktury sieci wirtualnej, ważne jest zrozumienie typów przepływów komunikacji sieciowej występujących w App Service Environment.

## <a name="general-network-flow"></a>Ogólny przepływ sieci
Gdy App Service Environment (ASE) używa publicznego wirtualnego adresu IP (VIP) dla aplikacji, cały ruch przychodzący jest docierany do tego publicznego adresu VIP.  Obejmuje to ruch HTTP i HTTPS dla aplikacji, a także inny ruch związany z usługą FTP, funkcją zdalnego debugowania i operacjami zarządzania platformy Azure.  Aby zapoznać się z pełną listą określonych portów (wymagane i opcjonalne), które są dostępne w publicznym adresie IP, zobacz artykuł dotyczący [kontrolowania ruchu przychodzącego][controllinginboundtraffic] do App Service Environment. 

Środowiska App Service obsługują również uruchamianie aplikacji, które są powiązane tylko z adresem wewnętrznym sieci wirtualnej, nazywanym również adresem ILB (wewnętrznym modułem równoważenia obciążenia).  Na ILB z włączonym ruchem środowiska ASE, HTTP i HTTPS dla aplikacji, jak również wywołania zdalnego debugowania, docierają do adresu ILB.  W przypadku najczęściej używanych konfiguracji ILB-ASE ruch FTP/FTPS zostanie również wyświetlony na adresie ILB.  Jednak operacje zarządzania platformy Azure będą nadal przepływać do portów 454/455 w publicznym adresie IP ILB środowiska ASE.

Na poniższym diagramie przedstawiono omówienie różnych przepływów sieci przychodzących i wychodzących dla App Service Environment, w przypadku których aplikacje są powiązane z publicznym wirtualnym adresem IP:

![Ogólne przepływy sieciowe][GeneralNetworkFlows]

App Service Environment może komunikować się z różnymi prywatnymi punktami końcowymi klientów.  Na przykład aplikacje działające w App Service Environment mogą łączyć się z serwerami baz danych uruchomionymi na maszynach wirtualnych IaaS w tej samej topologii sieci wirtualnej.

> [!IMPORTANT]
> Podczas przeglądania diagramu sieci "inne zasoby obliczeniowe" są wdrażane w innej podsieci niż App Service Environment. Wdrożenie zasobów w tej samej podsieci przy użyciu środowiska ASE spowoduje zablokowanie łączności z platformą ASE z tymi zasobami (z wyjątkiem określonego routingu w środowisku ASE). W zamian Wdróż w inną podsieć (w tej samej sieci wirtualnej). Następnie App Service Environment będzie mógł nawiązać połączenie. Żadna dodatkowa konfiguracja nie jest wymagana.
> 
> 

Środowiska App Servicee komunikują się również z zasobami usługi SQL DB i Azure Storage, które są niezbędne do zarządzania i obsługi App Service Environment.  Niektóre z zasobów SQL i magazynu, z którymi App Service Environment się komunikują, znajdują się w tym samym regionie co App Service Environment, podczas gdy inne znajdują się w odległych regionach platformy Azure.  W związku z tym połączenie wychodzące do Internetu jest zawsze wymagane do poprawnego działania App Service Environment. 

Ponieważ App Service Environment jest wdrożona w podsieci, grupy zabezpieczeń sieci mogą służyć do kontrolowania ruchu przychodzącego do podsieci.  Aby uzyskać szczegółowe informacje na temat sterowania ruchem przychodzącym do App Service Environment, zobacz następujący [artykuł][controllinginboundtraffic].

Aby uzyskać szczegółowe informacje na temat sposobu zezwalania na wychodzące połączenie internetowe z App Service Environment, zobacz następujący artykuł dotyczący pracy z usługą [Express Route][ExpressRoute].  To samo podejście opisane w artykule ma zastosowanie podczas pracy z połączeniem lokacja-lokacja i korzystania z wymuszonego tunelowania.

## <a name="outbound-network-addresses"></a>Wychodzące adresy sieciowe
Gdy App Service Environment wykonuje wywołania wychodzące, adres IP zawsze jest skojarzony z wywołaniami wychodzącymi.  Określony adres IP, który jest używany, zależy od tego, czy wywoływany punkt końcowy znajduje się w topologii sieci wirtualnej, czy poza topologią sieci wirtualnej.

Jeśli wywoływany punkt końcowy znajduje się **poza** topologią sieci wirtualnej, adres wychodzący (alias wychodzącego adresu NAT) jest używany jako publiczny adres VIP App Service Environment.  Ten adres można znaleźć w interfejsie użytkownika portalu dla App Service Environment w bloku właściwości.

![Wychodzący adres IP][OutboundIPAddress]

Ten adres można również ustalić dla środowisk ASE, który ma tylko publiczny adres VIP, tworząc aplikację w App Service Environment, a następnie wykonując polecenie *nslookup* na adresie aplikacji. Wynikowy adres IP jest zarówno publicznym adresem VIP, jak i wychodzącym adresem NAT App Service Environment.

Jeśli wywoływany punkt końcowy jest **wewnątrz** topologii sieci wirtualnej, adres wychodzący aplikacji wywołującej będzie wewnętrznym adresem IP indywidualnego zasobu obliczeniowego, na którym działa aplikacja.  Nie istnieje jednak trwałe mapowanie wewnętrznych adresów IP sieci wirtualnej do aplikacji.  Aplikacje mogą poruszać się w różnych zasobach obliczeniowych, a pula dostępnych zasobów obliczeniowych w App Service Environment może ulec zmianie ze względu na operacje skalowania.

Jednak ponieważ App Service Environment zawsze znajduje się w podsieci, masz gwarancję, że wewnętrzny adres IP zasobu obliczeniowego, na którym działa aplikacja, będzie zawsze znajdować się w zakresie CIDR podsieci.  W związku z tym, gdy szczegółowe listy ACL lub sieciowe grupy zabezpieczeń są używane w celu zabezpieczenia dostępu do innych punktów końcowych w sieci wirtualnej, zakres podsieci zawierający App Service Environment musi mieć przyznane prawo dostępu.

Na poniższym diagramie przedstawiono te koncepcje bardziej szczegółowo:

![Wychodzące adresy sieciowe][OutboundNetworkAddresses]

Na powyższym diagramie:

* Ponieważ publiczne adresy VIP App Service Environment to 192.23.1.2, to jest wychodzący adres IP używany podczas wykonywania wywołań do punktów końcowych "Internet".
* Zakres CIDR podsieci zawierającej App Service Environment to 10.0.1.0/26.  W przypadku innych punktów końcowych w ramach tej samej infrastruktury sieci wirtualnej będą widoczne wywołania z aplikacji pochodzące z dowolnego miejsca w ramach tego zakresu adresów.

## <a name="calls-between-app-service-environments"></a>Wywołania między środowiskami App Service
Bardziej skomplikowany scenariusz może wystąpić w przypadku wdrożenia wielu środowisk App Service w tej samej sieci wirtualnej i wychodzące wywołania z jednego App Service Environment do innego App Service Environment.  Te typy wywołań krzyżowych App Service Environment również będą traktowane jako wywołania "Internet".

Na poniższym diagramie przedstawiono przykład architektury warstwowej z aplikacjami na jednym App Service Environment (np. "aplikacje sieci Web" z przodu ") wywoływanie aplikacji na drugim App Service Environment (np. wewnętrzne aplikacje interfejsu API zaplecza, które nie są przeznaczone do dostępności z Internetu). 

![Wywołania między środowiskami App Service][CallsBetweenAppServiceEnvironments] 

W przykładzie powyżej App Service Environment "ASE one" ma wychodzący adres IP 192.23.1.2.  Jeśli aplikacja uruchomiona na tym App Service Environment wykonuje wychodzące wywołanie do aplikacji uruchomionej w drugim App Service Environment ("ASE dwa") znajdującej się w tej samej sieci wirtualnej, wywołanie wychodzące będzie traktowane jako wywołanie "Internet".  W efekcie ruch sieciowy odbierany na drugim App Service Environment będzie widoczny jako pochodzący z 192.23.1.2 (tj. nie jest to zakres adresów podsieci pierwszego App Service Environment).

Mimo że wywołania między różnymi środowiskami App Service są traktowane jako wywołania "Internet", gdy oba środowiska App Service znajdują się w tym samym regionie świadczenia usługi Azure, ruch sieciowy pozostanie w regionalnej sieci platformy Azure i nie będzie fizycznie przepływał przez publiczny Internet.  W związku z tym można użyć sieciowej grupy zabezpieczeń w podsieci drugiej App Service Environment, aby zezwolić tylko na wywołania przychodzące z pierwszego App Service Environment (którego wychodzący adres IP to 192.23.1.2), co zapewnia bezpieczną komunikację między środowiskami App Service.

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
Szczegółowe informacje na temat portów ruchu przychodzącego używanych przez środowiska App Service i usługi Network Security Groups do kontrolowania ruchu przychodzącego są dostępne [tutaj][controllinginboundtraffic].

Szczegółowe informacje na temat korzystania z tras zdefiniowanych przez użytkownika w celu przyznania wychodzącego dostępu do Internetu w środowiskach App Service jest dostępnych w tym [artykule][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

