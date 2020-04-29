---
title: Równoważenie obciążenia platformy Azure przed drzwiami dzięki pakietowi dostarczania aplikacji platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje o tym, jak platforma Azure zaleca Równoważenie obciążenia za pomocą swojego zestawu dostarczania aplikacji
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 44af14a01e7b045b7abb6a84db89a67f3dd22445
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875286"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Równoważenie obciążenia za pomocą pakietu dostarczania aplikacji platformy Azure

## <a name="introduction"></a>Wprowadzenie
Microsoft Azure udostępnia wiele usług globalnych i regionalnych do zarządzania sposobem dystrybuowania i równoważenia obciążenia ruchu sieciowego: Traffic Manager, z przodu, Application Gateway i Load Balancer.  Wraz z wieloma regionami i architekturą platformy Azure za pomocą tych usług wspólnie można tworzyć niezawodne, skalowalne aplikacje o wysokiej wydajności.

![Pakiet dostarczania aplikacji ][1]
 
Te usługi są podzielone na dwie kategorie:
1. **Globalne usługi równoważenia obciążenia** , takie jak Traffic Manager i drzwiczki, dystrybuują ruch od użytkowników końcowych w regionalnych punktach końcowych, między chmurami, a nawet hybrydowymi usługami lokalnymi. Globalne Równoważenie obciążenia kieruje ruch do najbliższej zaplecze usługi i reaguje na zmiany niezawodności usługi lub wydajności, aby zachować maksymalną wydajność użytkowników. 
2. **Regionalne usługi równoważenia obciążenia** , takie jak usługa Load Balancer w warstwie Standardowa lub Application Gateway, umożliwiają dystrybucję ruchu w obrębie sieci wirtualnych (sieci wirtualnych) między maszynami wirtualnymi lub punktami końcowymi usługi zona w regionie.

Połączenie usług globalnych i regionalnych w aplikacji zapewnia kompleksową, wydajną i bezpieczną metodę kierowania ruchu do i od użytkowników do usług IaaS, PaaS i lokalnych. W następnej sekcji opisano każdą z tych usług.

## <a name="global-load-balancing"></a>Globalne Równoważenie obciążenia
**Traffic Manager** zapewnia globalne Równoważenie obciążenia DNS. Sprawdza przychodzące żądania DNS i reaguje na w dobrej wewnętrznej bazie danych, zgodnie z zasadami routingu wybranymi przez klienta. Opcje dotyczące metod routingu są następujące:
- Routing wydajności do wysyłania żądania do najbliższej zaplecza w warunkach opóźnienia.
- Priorytetowe kierowanie umożliwiające kierowanie całego ruchu do zaplecza, z innymi zakończonymi jako kopia zapasowa.
- Ważony Routing działający w trybie okrężnym, który dystrybuuje ruch na podstawie wagi przypisanej do każdego zaplecza.
- Routing geograficzny, aby zapewnić, że osoby żądające znajdujące się w określonych regionach geograficznych są kierowane do założeń zamapowanych na te regiony (na przykład wszystkie żądania z Hiszpanii powinny być kierowane do regionu centralnego platformy Azure)
- Routing podsieci, który umożliwia mapowanie zakresów adresów IP na zaplecza, tak aby żądania pochodzące z tych elementów były wysyłane do określonego zaplecza (na przykład wszyscy użytkownicy łączący się z zakresu adresów IP firmowej CENTRALĄ powinni uzyskać inną zawartość sieci Web niż ogólna liczba użytkowników).

Klient łączy się bezpośrednio z tym zapleczem. Usługa Azure Traffic Manager wykrywa, kiedy wewnętrzna baza danych jest w złej kondycji, a następnie przekierowuje klientów do innego wystąpienia w dobrej kondycji. Zapoznaj się z dokumentacją usługi [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) , aby dowiedzieć się więcej o usłudze.

**Drzwi frontonu platformy Azure** udostępniają przyspieszenie dynamiczne witryny sieci Web (DSA), w tym globalne Równoważenie obciążenia HTTP.  Wygląda na to, że przychodzące żądania HTTP kierują do najbliższej zaplecza usługi/regionu dla określonej nazwy hosta, ścieżki URL i skonfigurowanych reguł.  
Drzwi czołowe kończą żądania HTTP na granicy sieci firmy Microsoft i aktywnie sonduje w celu wykrywania kondycji aplikacji lub infrastruktury lub zmiany czasu oczekiwania.  Przód drzwi zawsze kieruje ruch do najszybszych i dostępnych (zdrowych) zaplecza. Aby dowiedzieć się więcej o usłudze, zapoznaj się z informacjami o [architekturze routingu](front-door-routing-architecture.md) frontonu i [metodami routingu ruchu](front-door-routing-methods.md) .

## <a name="regional-load-balancing"></a>Lokalne Równoważenie obciążenia
Application Gateway udostępnia kontroler dostarczania aplikacji (ADC) jako usługę, oferując różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Umożliwia ona klientom Optymalizowanie wydajności farmy sieci Web dzięki przeciążeniu przerwania procesora TLS intensywnie wykorzystującego procesor do bramy aplikacji. Inne możliwości routingu warstwy 7 obejmują dystrybucję rozruchową ruchu przychodzącego, koligację sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za pojedynczą bramą aplikacji. Application Gateway można skonfigurować jako bramę internetową, bramę tylko wewnętrzną lub kombinację obu tych elementów. Application Gateway jest w pełni zarządzana, skalowalna i wysoka dostępność na platformie Azure. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie.
Load Balancer to integralna część stosu usługi Azure SDN, zapewniająca wysoką wydajność i niskie opóźnienia warstwy 4 usługi równoważenia obciążenia dla wszystkich protokołów UDP i TCP. Służy do zarządzania połączeniami przychodzącymi i wychodzącymi. Możesz skonfigurować publiczne i wewnętrzne punkty końcowe ze zrównoważonym obciążeniem i zdefiniować reguły mapowania połączeń przychodzących do miejsc docelowych w puli zaplecza, wykorzystując opcje badania kondycji protokołu TCP i HTTP do zarządzania dostępnością usługi.


## <a name="choosing-a-global-load-balancer"></a>Wybieranie globalnego modułu równoważenia obciążenia
W przypadku wybrania globalnego modułu równoważenia obciążenia między Traffic Manager i drzwiami frontonu platformy Azure dla routingu globalnego należy wziąć pod uwagę, co jest podobne i jakie są różne dla tych dwóch usług.   Obie usługi zapewniają
- **Nadmiarowość geograficzna:** Jeśli jeden region ulegnie awarii, ruch bezproblemowo przekierowuje do najbliższego regionu bez żadnej interwencji od właściciela aplikacji.
- **Routing najbliższych regionów:** Ruch jest automatycznie kierowany do najbliższego regionu

</br>W poniższej tabeli opisano różnice między Traffic Manager i środowiskiem frontonu platformy Azure:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Dowolny protokół:** Ponieważ Traffic Manager działa w warstwie DNS, można kierować dowolnego typu ruchu sieciowego. HTTP, TCP, UDP itd. | **Przyspieszenie http:** Ruch z przodu jest serwerem proxy na granicy sieci firmy Microsoft.  Z tego powodu żądania HTTP (S) zobaczą opóźnienia i usprawnienia przepływności zmniejszają opóźnienia dla negocjacji TLS i używają aktywnych połączeń z AFD do aplikacji.|
|**Routing lokalny:** W przypadku routingu w warstwie DNS ruch zawsze przechodzi od punktu do punktu.  Routing z biura oddziału do lokalnego centrum danych może mieć ścieżkę bezpośrednią; nawet we własnej sieci przy użyciu Traffic Manager. | **Niezależna skalowalność:** Ze względu na to, że drzwi czołowe współdziałają z żądaniem HTTP, żądania do różnych ścieżek URL mogą być kierowane do różnych pul usług zaplecza/regionalnej (mikrousługi) na podstawie zasad i kondycji każdej mikrousługi aplikacji.|
|**Format rozliczeń:** Rozliczenia oparte na systemie DNS są skalowane dla użytkowników i dla usług o większej liczbie użytkowników, co pozwala zmniejszyć koszty przy użyciu wyższego poziomu. |**Zabezpieczenia wbudowane:** Drzwi tylne umożliwiają stosowanie takich reguł jak ograniczanie szybkości i listy ACL adresów IP — w celu zapewnienia ochrony frontonu przed przystąpieniem do aplikacji. 

</br>Ze względu na wydajność, gotowość i zabezpieczenia dla obciążeń HTTP z przodu, zalecamy klientom korzystanie z czołowych drzwi do obciążeń protokołu HTTP.    Traffic Manager i drzwi przednich mogą być używane równolegle do obsługi całego ruchu dla aplikacji. 

## <a name="building-with-azures-application-delivery-suite"></a>Kompilowanie przy użyciu pakietu dostarczania aplikacji platformy Azure 
Firma Microsoft zaleca, aby wszystkie witryny sieci Web, interfejsy API, usługi były geograficznie nadmiarowe i dostarczać ruch do swoich użytkowników z najbliższej lokalizacji (najniższego opóźnienia) do tych, gdy jest to możliwe.  Łączenie usług z Traffic Manager, z przodu, Application Gateway i Load Balancer umożliwia tworzenie geograficznie i zonally nadmiarowe w celu zmaksymalizowania niezawodności, skali i wydajności.

Na poniższym diagramie opisano przykładową usługę, która używa kombinacji wszystkich tych usług w celu utworzenia globalnej usługi sieci Web.   W takim przypadku architekt używa Traffic Manager do kierowania globalnie do globalnych punktów końcowych do dostarczania plików i obiektów, przy jednoczesnym użyciu drzwi do globalnych tras adresów URL, które pasują do wzorca/Store/* do usługi, do której zostały zmigrowane, App Service podczas routingu wszystkich innych żądań do regionalnych bram aplikacji.

W regionie dla usługi IaaS, Deweloper aplikacji stwierdził, że wszystkie adresy URL zgodne z wzorcem/images/* są obsługiwane przez dedykowaną pulę maszyn wirtualnych, które różnią się od reszty kolektywu serwerów sieci Web.

Ponadto domyślna pula maszyn wirtualnych obsługująca zawartość dynamiczną musi komunikować się z bazą danych zaplecza hostowaną w klastrze o wysokiej dostępności. Całe wdrożenie zostało skonfigurowane za pomocą Azure Resource Manager.

Na poniższym diagramie przedstawiono architekturę tego scenariusza:

![Szczegółowa Architektura pakietu dostarczania aplikacji][2] 

> [!NOTE]
> Ten przykład dotyczy tylko jednej z wielu możliwych konfiguracji usług równoważenia obciążenia oferowanej przez platformę Azure. Traffic Manager, przód, Application Gateway i Load Balancer mogą być mieszane i dopasowywane do potrzeb związanych z równoważeniem obciążenia. Na przykład jeśli przetwarzanie za pośrednictwem protokołu TLS/SSL lub z warstwy 7 nie jest konieczne, Load Balancer mogą być używane zamiast Application Gateway.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
