---
title: Zagadnienia dotyczące topologii sieci w usłudze Azure serwer proxy aplikacji usługi Azure AD
description: Omawia zagadnienia dotyczące topologii sieci podczas korzystania z usługi Azure serwer proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d814a3ce62d7e9b3ce04953e05293d17ecc08d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764472"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Zagadnienia dotyczące topologii sieci podczas korzystania z serwer proxy aplikacji usługi Azure Active Directory

W tym artykule opisano zagadnienia dotyczące topologii sieci podczas korzystania z serwera proxy aplikacji Azure Active Directory (Azure AD) do zdalnego publikowania i uzyskiwania dostępu do aplikacji.

## <a name="traffic-flow"></a>Przepływ ruchu

Gdy aplikacja jest publikowana za pośrednictwem usługi Azure serwer proxy aplikacji usługi Azure AD, ruch od użytkowników do aplikacji przechodzi przez trzy połączenia:

1. Użytkownik nawiązuje połączenie z publicznym punktem końcowym usługi Azure serwer proxy aplikacji usługi Azure AD na platformie Azure
1. Usługa serwera proxy aplikacji nawiązuje połączenie z łącznikiem serwera proxy aplikacji
1. Łącznik serwera proxy aplikacji nawiązuje połączenie z aplikacją docelową

![Diagram przedstawiający przepływ ruchu od użytkownika do aplikacji docelowej](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Lokalizacja dzierżawy i usługa serwera proxy aplikacji

Gdy zarejestrujesz się w celu uzyskania dzierżawy usługi Azure AD, region dzierżawy jest określany na podstawie określonego kraju/regionu. Po włączeniu serwera proxy aplikacji wystąpienia usługi serwera proxy aplikacji dla Twojej dzierżawy są wybierane lub tworzone w tym samym regionie, w którym znajduje się dzierżawa usługi Azure AD lub w najbliższym regionie.

Jeśli na przykład kraj lub region dzierżawy usługi Azure AD jest w Zjednoczonym Królestwie, wszystkie łączniki serwera proxy aplikacji używają wystąpień usługi w europejskich centrach danych. Gdy użytkownicy uzyskują dostęp do opublikowanych aplikacji, ruch przechodzi przez wystąpienia usługi serwera proxy aplikacji w tej lokalizacji.

## <a name="considerations-for-reducing-latency"></a>Zagadnienia dotyczące zmniejszenia opóźnień

Wszystkie rozwiązania serwera proxy wprowadzają opóźnienia do połączenia sieciowego. Niezależnie od tego, które rozwiązanie serwera proxy lub sieci VPN zostało wybrane jako rozwiązanie dostępu zdalnego, zawsze zawiera zestaw serwerów umożliwiających połączenie z siecią firmową.

Organizacje zwykle obejmują punkty końcowe serwera w ich sieci obwodowej. Na platformie Azure serwer proxy aplikacji usługi Azure AD jednak ruch przepływów przez usługę serwera proxy w chmurze, gdy łączniki znajdują się w sieci firmowej. Sieć obwodowa nie jest wymagana.

Kolejne sekcje zawierają dodatkowe sugestie ułatwiające jeszcze dalsze zmniejszenie opóźnienia. 

### <a name="connector-placement"></a>Położenie łącznika

Serwer proxy aplikacji wybiera lokalizację wystąpień, w zależności od lokalizacji dzierżawy. Jednak możesz zdecydować, gdzie zainstalować łącznik, co pozwala na zdefiniowanie charakterystyki opóźnienia ruchu sieciowego.

Podczas konfigurowania usługi serwera proxy aplikacji należy zadać następujące pytania:

- Gdzie znajduje się aplikacja?
- Gdzie znajdują się większość użytkowników, którzy uzyskują dostęp do aplikacji?
- Gdzie znajduje się wystąpienie serwera proxy aplikacji?
- Czy masz już dedykowane połączenie sieciowe do centrów danych platformy Azure, takich jak Azure ExpressRoute czy podobna sieć VPN?

Łącznik musi komunikować się zarówno z platformą Azure, jak i aplikacjami (kroki 2 i 3 w diagramie przepływu ruchu), dzięki czemu rozmieszczenie łącznika ma wpływ na opóźnienie tych dwóch połączeń. Podczas oceniania położenia łącznika należy pamiętać o następujących kwestiach:

- Jeśli chcesz użyć ograniczonego delegowania protokołu Kerberos (KCD) dla logowania jednokrotnego, łącznik wymaga linii wglądu w centrum danych. Ponadto serwer łączników musi być przyłączony do domeny.  
- W razie wątpliwości Zainstaluj łącznik bliżej aplikacji.

### <a name="general-approach-to-minimize-latency"></a>Ogólne podejście do minimalizowania opóźnień

Możesz zminimalizować opóźnienia ruchu końcowego przez optymalizację poszczególnych połączeń sieciowych. Każde połączenie może być zoptymalizowane przez:

- Zmniejszenie odległości między dwoma końcami przeskoku.
- Wybieranie odpowiedniej sieci do przechodzenia. Na przykład przechodzenie sieci prywatnej zamiast publicznego Internetu może być szybsze, ze względu na dedykowane linki.

Jeśli masz dedykowany link sieci VPN lub ExpressRoute między platformą Azure i siecią firmową, możesz użyć tego programu.

## <a name="focus-your-optimization-strategy"></a>Skupienie się na strategii optymalizacji

Istnieje niewielkie możliwości kontrolowania połączenia między użytkownikami i usługą serwera proxy aplikacji. Użytkownicy mogą uzyskiwać dostęp do aplikacji w sieci domowej, w sklepie kawowym lub w innym kraju/regionie. Zamiast tego można zoptymalizować połączenia z usługi serwera proxy aplikacji do łączników serwera proxy aplikacji w aplikacjach. Rozważ uwzględnienie następujących wzorców w środowisku.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Wzorzec 1: Umieść łącznik blisko aplikacji

Umieść łącznik blisko aplikacji docelowej w sieci klienta. Ta konfiguracja minimalizuje krok 3 na diagramie topografii, ponieważ łącznik i aplikacja są zamknięte.

Jeśli łącznik wymaga linii wglądu do kontrolera domeny, ten wzorzec jest korzystne. Większość naszych klientów korzysta z tego wzorca, ponieważ dobrze sprawdza się w większości scenariuszy. Ten wzorzec może być również połączony z wzorcem 2 w celu optymalizacji ruchu między usługą i łącznikiem.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Wzorzec 2: korzystanie z ExpressRoute z usługą komunikacji równorzędnej firmy Microsoft

Jeśli masz ExpressRoute skonfigurowany przy użyciu komunikacji równorzędnej firmy Microsoft, możesz użyć szybszego połączenia ExpressRoute dla ruchu między serwerem proxy aplikacji a łącznikiem. Łącznik nadal znajduje się w Twojej sieci, zamknij aplikację.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Wzorzec 3: korzystanie z ExpressRoute z prywatną komunikację równorzędną

Jeśli masz dedykowaną sieć VPN lub ExpressRoute konfigurację z prywatną komunikację równorzędną między platformą Azure i siecią firmową, masz inną opcję. W tej konfiguracji Sieć wirtualna na platformie Azure jest zwykle traktowana jako rozszerzenie sieci firmowej. Możesz również zainstalować łącznik w centrum danych platformy Azure i nadal spełniać wymagania dotyczące małych opóźnień połączenia łącznik-aplikacja.

Opóźnienie nie zostało naruszone, ponieważ ruch przepływów przez dedykowane połączenie. Możesz również uzyskać ulepszone opóźnienie usługi serwera proxy aplikacji, ponieważ łącznik jest instalowany w centrum danych platformy Azure w pobliżu lokalizacji dzierżawy usługi Azure AD.

![Diagram przedstawiający łącznik zainstalowany w centrum danych platformy Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Inne podejścia

Mimo że ten artykuł jest rozmieszczeniem łączników, możesz również zmienić umieszczanie aplikacji w celu uzyskania lepszych cech opóźnienia.

Coraz bardziej organizacje przenoszą swoje sieci do hostowanych środowisk. Dzięki temu można umieszczać aplikacje w środowisku hostowanym, które jest również częścią sieci firmowej, a nadal znajdować się w domenie. W takim przypadku wzorce omówione w poprzednich sekcjach można zastosować do nowej lokalizacji aplikacji. Jeśli rozważasz tę opcję, zobacz [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

Ponadto należy rozważyć zorganizowanie łączników przy użyciu [grup łączników](application-proxy-connector-groups.md) do aplikacji docelowych, które znajdują się w różnych lokalizacjach i sieciach.

## <a name="common-use-cases"></a>Typowe przypadki użycia

W tej sekcji omówiono kilka typowych scenariuszy. Załóżmy, że dzierżawa usługi Azure AD (i w związku z tym punkt końcowy usługi proxy) znajduje się w Stany Zjednoczone (US). Zagadnienia omówione w tych przypadkach użycia mają zastosowanie również do innych regionów na całym świecie.

W tych scenariuszach nazywamy każde połączenie "przeskok" i podziel je na łatwiejsze dyskusje:

- **Przeskok 1**: użytkownik do usługi serwera proxy aplikacji
- **Przeskok 2**: usługa serwera proxy aplikacji do łącznika serwera proxy aplikacji
- **Przeskok 3**: łącznik serwera proxy aplikacji do aplikacji docelowej 

### <a name="use-case-1"></a>Przypadek użycia 1

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w Stanach Zjednoczonych, z użytkownikami w tym samym regionie. Między centrum danych platformy Azure a siecią firmową nie istnieje ExpressRoute ani sieć VPN.

**Zalecenie:** Obserwuj wzorzec 1, wyjaśniono w poprzedniej sekcji. W celu uzyskania ulepszonego opóźnienia Rozważ użycie ExpressRoute, w razie potrzeby.

Jest to prosty wzorzec. Możesz zoptymalizować skok 3 przez umieszczenie łącznika w sąsiedztwie aplikacji. Jest to również naturalny wybór, ponieważ łącznik zazwyczaj jest instalowany z wierszem wglądu do aplikacji i centrum danych w celu wykonywania operacji KCD.

![Diagram przedstawiający użytkowników, serwer proxy, łącznik i aplikację są w Stanach Zjednoczonych](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Przypadek użycia 2

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w Stanach Zjednoczonych, a użytkownicy są rozproszeni globalnie. Między centrum danych platformy Azure a siecią firmową nie istnieje ExpressRoute ani sieć VPN.

**Zalecenie:** Obserwuj wzorzec 1, wyjaśniono w poprzedniej sekcji.

Często typowym wzorcem jest Optymalizowanie skoku 3, gdzie umieszczasz łącznik blisko aplikacji. Skok 3 nie jest zazwyczaj kosztowny, jeśli jest to wszystko w tym samym regionie. Jednak skok 1 może być droższy w zależności od tego, gdzie użytkownik jest, ponieważ użytkownicy na całym świecie muszą uzyskiwać dostęp do wystąpienia serwera proxy aplikacji w Stanach Zjednoczonych. Warto zauważyć, że każde rozwiązanie proxy ma podobną charakterystykę dotyczącą użytkowników rozmieszczonych globalnie.

![Użytkownicy są rozproszeni globalnie, ale wszystkie inne elementy znajdują się w Stanach Zjednoczonych](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Przypadek użycia 3

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w Stanach Zjednoczonych. Na platformie Azure i w sieci firmowej istnieje ExpressRoute z usługą komunikacji równorzędnej firmy Microsoft.

**Zalecenie:** Obserwuj wzorce 1 i 2, wyjaśniono w poprzedniej sekcji.

Najpierw umieść łącznik jak najbliżej aplikacji. Następnie system automatycznie używa ExpressRoute dla skoku 2.

Jeśli łącze ExpressRoute korzysta z komunikacji równorzędnej firmy Microsoft, ruch między serwerem proxy a łącznikiem jest przenoszony przez ten link. Przeskok 2 ma zoptymalizowane opóźnienia.

![Diagram przedstawiający ExpressRoute między serwerem proxy i łącznikiem](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Przypadek użycia 4

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w Stanach Zjednoczonych. Na platformie Azure i w sieci firmowej istnieje ExpressRoute z prywatną komunikację równorzędną.

**Zalecenie:** Obserwuj wzorzec 3, wyjaśniono w poprzedniej sekcji.

Umieść łącznik w centrum danych platformy Azure, który jest połączony z siecią firmową za pomocą prywatnej komunikacji równorzędnej ExpressRoute.

Łącznik można umieścić w centrum danych platformy Azure. Ponieważ łącznik nadal ma linię wglądu w aplikację i centrum danych za pomocą sieci prywatnej, przeskok 3 jest zoptymalizowany. Ponadto przeskok 2 jest zoptymalizowany pod kątem dalszej optymalizacji.

![Łącznik w centrum danych platformy Azure, ExpressRoute między łącznikiem a aplikacją](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Przypadek użycia 5

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w Europie, z wystąpieniem serwera proxy aplikacji i większością użytkowników w Stanach Zjednoczonych.

**Zalecenie:** Umieść łącznik blisko aplikacji. Ponieważ wszyscy użytkownicy uzyskują dostęp do wystąpienia serwera proxy aplikacji, który ma miejsce w tym samym regionie, przeskok 1 nie jest zbyt kosztowny. Przeskok 3 jest zoptymalizowany. Rozważ użycie ExpressRoute do optymalizacji skoku 2.

![Diagram przedstawia użytkowników i serwer proxy w Stanach Zjednoczonych, łączniku i aplikacji w Europie](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

W tej sytuacji można również rozważyć użycie jednego innego wariantu. Jeśli większość użytkowników w organizacji znajduje się w Stanach Zjednoczonych, prawdopodobnie jest to, że Twoja sieć również zostanie rozszerzona do Stanów Zjednoczonych. Umieść łącznik w Stanach Zjednoczonych i użyj dedykowanej wewnętrznej linii firmowej sieci do aplikacji w Europie. W ten sposób są optymalizowane przeskoki 2 i 3.

![Diagram przedstawia użytkowników, proxy i łącznik w Stanach Zjednoczonych, aplikacji w Europie](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Następne kroki

- [Włącz serwer proxy aplikacji](application-proxy-add-on-premises-application.md)
- [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
- [Włącz dostęp warunkowy](application-proxy-integrate-with-sharepoint-server.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md)
