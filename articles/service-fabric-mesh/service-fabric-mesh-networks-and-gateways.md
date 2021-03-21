---
title: Wprowadzenie do usługi Azure Service Fabric Networking
description: Informacje na temat sieci, bram i inteligentnego routingu ruchu w Service Fabric siatki.
author: georgewallace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: ef8b40be366afe24b960f7419d84cc2a9976b592
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625705"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Wprowadzenie do sieci w aplikacjach Service Fabric siatki

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

W tym artykule opisano różne typy modułów równoważenia obciążenia, sposób łączenia sieci z aplikacjami z innymi sieciami oraz sposób kierowania ruchu między usługami w aplikacjach.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Moduły równoważenia obciążenia warstwy 4 vs warstwy 7
Równoważenie obciążenia można przeprowadzić na różnych warstwach w [modelu osi](https://en.wikipedia.org/wiki/OSI_model) sieci, często w warstwach 4 (P4) i w warstwie 7 (P7).  Zazwyczaj istnieją dwa typy modułów równoważenia obciążenia:

- Moduł równoważenia obciążenia P4 działa w warstwie transportu sieciowego, która zajmuje się dostarczaniem pakietów bez względu na zawartość pakietów. Moduł równoważenia obciążenia sprawdza tylko nagłówki pakietów, więc kryteria równoważenia są ograniczone do adresów IP i portów. Na przykład klient nawiązuje połączenie TCP z usługą równoważenia obciążenia. Moduł równoważenia obciążenia kończy połączenie (przez odpowiadanie bezpośrednio na SYN), wybranie zaplecza i nawiązanie nowego połączenia TCP z zapleczem (wysyła nowy SYN). Moduł równoważenia obciążenia P4 zazwyczaj działa tylko na poziomie połączenia TCP/UDP P4 lub sesji. W tym celu równoważenie obciążenia przekierowuje bajty i zapewnia, że bajty z tej samej sesji są zawijane w tym samym zaplecze. Moduł równoważenia obciążenia P4 jest nieświadomy wszelkich szczegółów aplikacji przenoszonych przez ten program. Bajty mogą być dowolnymi protokołami aplikacji.

- Moduł równoważenia obciążenia P7 działa w warstwie aplikacji, która zajmuje się zawartością każdego pakietu. Sprawdza zawartość pakietu, ponieważ rozumie protokoły takie jak HTTP, HTTPS lub WebSockets. Dzięki temu moduł równoważenia obciążenia może wykonywać zaawansowane Routing. Na przykład klient tworzy pojedyncze połączenie TCP/2 z usługą równoważenia obciążenia. Moduł równoważenia obciążenia następnie przechodzi do dwóch połączeń zaplecza. Gdy klient wysyła dwa strumienie protokołu HTTP/2 do modułu równoważenia obciążenia, strumień jeden jest wysyłany do zaplecza, a dwa strumienie są wysyłane do drugiego zaplecza. W ten sposób nawet multiplekser klientów, którzy znacznie różnią się obciążeniami żądań, będzie wydajnie zrównoważony przez założenia. 

## <a name="networks-and-gateways"></a>Sieci i bramy
W [modelu zasobów Service Fabric](service-fabric-mesh-service-fabric-resources.md)zasób sieciowy jest indywidualnie wdrażanym zasobem, niezależnie od zasobu aplikacji lub usługi, który może odwoływać się do niego jako zależność. Służy do tworzenia sieci dla aplikacji, które są otwarte dla Internetu. Wiele usług z różnych aplikacji może być częścią tej samej sieci. Ta sieć prywatna jest tworzona i zarządzana przez Service Fabric i nie jest siecią wirtualną platformy Azure. Aplikacje można dynamicznie dodawać i usuwać z zasobów sieciowych w celu włączenia i wyłączenia łączności sieci wirtualnej. 

Brama jest używana do mostkowania dwóch sieci. Zasób bramy wdraża [serwer proxy wysłannika](https://www.envoyproxy.io/) , który zapewnia Routing P4 dla dowolnego protokołu i routingu P7 dla zaawansowanego ROUTINGU aplikacji http (S). Brama kieruje ruch do sieci z sieci zewnętrznej i określa, do której usługi należy kierować ruchem.  Siecią zewnętrzną może być otwarta sieć (zasadniczo publiczny Internet) lub Sieć wirtualna platformy Azure, która umożliwia nawiązywanie połączeń z innymi aplikacjami i zasobami platformy Azure. 

![Sieć i Brama][Image1]

Gdy zasób sieciowy zostanie utworzony za pomocą `ingressConfig` programu, publiczny adres IP jest przypisywany do zasobu sieciowego. Publiczny adres IP zostanie powiązany z okresem istnienia zasobu sieciowego.

Gdy aplikacja siatkowa zostanie utworzona, powinna odwoływać się do istniejącego zasobu sieciowego. Nowe porty publiczne mogą być dodawane lub istniejące porty można usunąć z konfiguracji transferu danych przychodzących. Usunięcie zasobu sieciowego zakończy się niepowodzeniem, jeśli odwołuje się do niego zasób aplikacji. Po usunięciu aplikacji zasób sieciowy zostaje usunięty.

## <a name="next-steps"></a>Następne kroki 
Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, zapoznaj się z omówieniem:
- [Omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png