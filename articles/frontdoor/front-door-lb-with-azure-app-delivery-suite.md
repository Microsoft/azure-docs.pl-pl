---
title: Równoważenie obciążenia platformy Azure przed drzwiami dzięki pakietowi dostarczania aplikacji platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje o tym, jak platforma Azure zaleca Równoważenie obciążenia za pomocą swojego zestawu dostarczania aplikacji
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019467"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Równoważenie obciążenia za pomocą pakietu dostarczania aplikacji platformy Azure

## <a name="introduction"></a>Wprowadzenie
Microsoft Azure oferuje różne globalne i regionalne usługi do zarządzania sposobem dystrybuowania ruchu sieciowego i równoważenia obciążenia: 

* Application Gateway
* Front Door 
* Load Balancer  
* Traffic Manager

Wraz z wieloma regionami i architekturą strefowej platformy Azure korzystanie z tych usług razem może umożliwić tworzenie niezawodnych, skalowalnych i wysoko wydajnych aplikacji.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Pakiet dostarczania aplikacji":::
 
Te usługi są podzielone na dwie kategorie:
1. **Globalne usługi równoważenia obciążenia** , takie jak Traffic Manager i drzwiczki, dystrybuują ruch od użytkowników końcowych w regionalnych punktach końcowych, między chmurami, a nawet hybrydowymi usługami lokalnymi. Globalne Równoważenie obciążenia kieruje ruch do najbliższego zaplecza usługi i reaguje na zmiany w niezawodności usługi, aby zapewnić stałą dostępność i wysoką wydajność dla użytkowników. 
1. **Regionalne usługi równoważenia obciążenia** , takie jak moduły równoważenia obciążenia i bramy aplikacji, zapewniają możliwość dystrybucji ruchu do maszyn wirtualnych w ramach sieci wirtualnej (sieci wirtualnych) lub punktów końcowych usługi w obrębie regionu.

Łącząc te usługi globalne i regionalne, aplikacja będzie korzystać z niezawodnego i bezpiecznego, kompleksowego ruchu, który jest wysyłany od użytkowników końcowych do usług IaaS, PaaS lub lokalnych. W następnej sekcji opisano każdą z tych usług.

## <a name="global-load-balancing"></a>Globalne Równoważenie obciążenia
**Traffic Manager** zapewnia globalne Równoważenie obciążenia DNS. Sprawdza przychodzące żądania DNS i reaguje na w dobrej wewnętrznej bazie danych, zgodnie z zasadami routingu wybranymi przez klienta. Opcje dotyczące metod routingu są następujące:
- * * Routing wydajności wysyła żądania do najbliższej wewnętrznej bazy danych z najmniejszym opóźnieniem.
- **Kierowanie priorytetów** kieruje cały ruch do zaplecza, z innymi zakończonymi jako kopia zapasowa.
- **Rozważone Routing** działający w trybie okrężnym dystrybuuje ruch na podstawie wagi przypisanej do każdego zaplecza.
- **Routing geograficzny** gwarantuje, że żądania, które pochodzą z określonych regionów geograficznych, są obsługiwane przez aukcje zamapowane dla tych regionów. (Na przykład wszystkie żądania z Hiszpanii powinny być kierowane do regionu centralnego platformy Azure)
- **Routing podsieci** umożliwia mapowanie zakresów adresów IP na zaplecza, dzięki czemu żądania przychodzące dla tych adresów IP będą wysyłane do określonego zaplecza. (Na przykład wszyscy użytkownicy, którzy łączą się z zakresu adresów IP firmowej firmy CENTRALĄ, powinni uzyskać inną zawartość sieci Web niż ogólna użytkownicy)

Klient łączy się bezpośrednio z tym zapleczem. Usługa Azure Traffic Manager wykrywa, kiedy wewnętrzna baza danych jest w złej kondycji, a następnie przekierowuje klientów do innego wystąpienia w dobrej kondycji. Zapoznaj się z dokumentacją usługi [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) , aby dowiedzieć się więcej o usłudze.

**Drzwi frontonu platformy Azure** udostępniają przyspieszenie dynamiczne witryny sieci Web (DSA), w tym globalne Równoważenie obciążenia HTTP.  Wygląda na to, że przychodzące żądania HTTP kierują do najbliższej zaplecza usługi/regionu dla określonej nazwy hosta, ścieżki URL i skonfigurowanych reguł.  
Drzwi czołowe kończą żądania HTTP na granicy sieci firmy Microsoft i aktywnie sonduje w celu wykrywania kondycji aplikacji lub infrastruktury lub zmiany czasu oczekiwania.  Przód drzwi zawsze kieruje ruch do najszybszych i dostępnych (zdrowych) zaplecza. Aby dowiedzieć się więcej o usłudze, zapoznaj się z informacjami o [architekturze routingu](front-door-routing-architecture.md) frontonu i [metodami routingu ruchu](front-door-routing-methods.md) .

## <a name="regional-load-balancing"></a>Lokalne Równoważenie obciążenia
Application Gateway udostępnia kontroler dostarczania aplikacji (ADC) jako usługę, oferując różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Umożliwia ona klientom Optymalizowanie wydajności farmy sieci Web dzięki przeciążeniu przerwania procesora TLS intensywnie wykorzystującego procesor do bramy aplikacji. Inne dodatkowe możliwości routingu warstwy 7 obejmują również dystrybucję rozruchową ruchu przychodzącego, koligację sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za pojedynczą bramą aplikacji. Application Gateway można skonfigurować jako internetowy punkt końcowy, tylko wewnętrzny punkt końcowy lub kombinację obu tych elementów. Application Gateway jest w pełni zarządzana przez platformę Azure, zapewniając skalowalność i wysoką dostępność. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie.

Usługi równoważenia obciążenia są integralną częścią stosu usługi Azure SDN, która zapewnia wysoką wydajność i niskie opóźnienia warstwy 4 dla wszystkich protokołów UDP i TCP. Można skonfigurować publiczne lub wewnętrzne punkty końcowe ze zrównoważonym obciążeniem, definiując reguły, które mapują połączenia przychodzące na pule zaplecza. Monitorowanie kondycji przy użyciu protokołu TCP lub HTTPS może pomóc w zarządzaniu dostępnością usługi.

## <a name="choosing-a-global-load-balancer"></a>Wybieranie globalnego modułu równoważenia obciążenia
W przypadku wybrania globalnego modułu równoważenia obciążenia między Traffic Manager i drzwiami frontonu platformy Azure dla routingu globalnego należy wziąć pod uwagę, co jest podobne i jakie są różne dla tych dwóch usług.   Obie usługi zapewniają
- **Nadmiarowość geograficzna:** Jeśli jeden region wyjdzie z usługi, ruch bezproblemowo przekierowuje do najbliższego regionu bez żadnej interwencji od właściciela aplikacji.
- **Routing najbliższych regionów:** Ruch jest automatycznie kierowany do najbliższego regionu

</br>W poniższej tabeli opisano różnice między Traffic Manager i środowiskiem frontonu platformy Azure:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Dowolny protokół:** Ponieważ Traffic Manager działa w warstwie DNS, można kierować dowolnego typu ruchu sieciowego. HTTP, TCP, UDP i tak dalej. | **Przyspieszenie http:** Z przodu drzwi ruch jest przesyłany przez serwer proxy na granicy sieci firmy Microsoft. Żądania HTTP/S zobaczą ulepszenia opóźnienia i przepływności, które zmniejszają opóźnienia w przypadku negocjacji TLS.|
|**Routing lokalny:** W przypadku routingu w warstwie DNS ruch zawsze przechodzi od punktu do punktu.  Routing z biura oddziału do lokalnego centrum danych może mieć ścieżkę bezpośrednią; nawet we własnej sieci przy użyciu Traffic Manager. | **Niezależna skalowalność:** Ze względu na to, że drzwi czołowe współdziałają z żądaniem HTTP, żądania do różnych ścieżek URL mogą być kierowane do różnych pul usług zaplecza/regionalnej (mikrousługi) na podstawie zasad i kondycji każdej mikrousługi aplikacji.|
|**Format rozliczeń:** Rozliczenia oparte na systemie DNS są skalowane dla użytkowników i dla usług o większej liczbie użytkowników, co pozwala zmniejszyć koszty przy użyciu wyższego poziomu. |**Zabezpieczenia wbudowane:** Drzwi tylne umożliwiają stosowanie takich reguł jak ograniczanie szybkości i listy ACL adresów IP — w celu zapewnienia ochrony frontonu przed przystąpieniem do aplikacji. 

</br>Zalecamy klientom korzystanie z czołowych drzwi na potrzeby obciążeń protokołu HTTP ze względu na wydajność, gotowość i zabezpieczenia, które są obsługiwane przez protokół HTTP z przodu. Traffic Manager i drzwi przednich mogą być używane równolegle do obsługi całego ruchu dla aplikacji. 

## <a name="building-with-azures-application-delivery-suite"></a>Kompilowanie przy użyciu pakietu dostarczania aplikacji platformy Azure 
Zalecamy, aby wszystkie witryny sieci Web, interfejsy API i usługi były geograficznie nadmiarowe, dzięki czemu mogą dostarczać ruch do swoich użytkowników z najbliższej lokalizacji, jeśli jest to możliwe.  Łączenie wielu usług równoważenia obciążenia umożliwia tworzenie nadmiarowości geograficznej i strefowej w celu zmaksymalizowania niezawodności i wydajności.

Na poniższym diagramie opisano przykładową architekturę, która używa kombinacji wszystkich tych usług w celu utworzenia globalnej usługi sieci Web. Architekt używa Traffic Manager, aby kierować ruch do globalnych frontonów dla dostarczania plików i obiektów. Korzystając z czołowych drzwi, można globalnie kierować ścieżki URL zgodne ze wzorcem/Store/* do usługi, do której zostały zmigrowane, do App Service. Wreszcie wszystkie inne żądania są routowane do regionalnych bram aplikacji.

W każdym regionie usługi IaaS, Deweloper aplikacji stwierdził, że wszystkie adresy URL zgodne z wzorcem/images/* są obsługiwane przez dedykowaną pulę maszyn wirtualnych. Ta Pula maszyn wirtualnych różni się od reszty kolektywu serwerów sieci Web.

Ponadto domyślna pula maszyn wirtualnych obsługująca zawartość dynamiczną musi komunikować się z bazą danych zaplecza hostowaną w klastrze o wysokiej dostępności. Całe wdrożenie jest konfigurowane za pomocą Azure Resource Manager.

Na poniższym diagramie przedstawiono architekturę tego scenariusza:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Szczegółowa Architektura pakietu dostarczania aplikacji":::

> [!NOTE]
> Ten przykład dotyczy tylko jednej z wielu możliwych konfiguracji usług równoważenia obciążenia oferowanej przez platformę Azure. Traffic Manager, przód, Application Gateway i Load Balancer mogą być mieszane i dopasowywane do potrzeb związanych z równoważeniem obciążenia. Na przykład jeśli przetwarzanie za pośrednictwem protokołu TLS/SSL lub z warstwy 7 nie jest konieczne, Load Balancer mogą być używane zamiast Application Gateway.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
