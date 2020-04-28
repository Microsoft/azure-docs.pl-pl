---
title: Standardowe strefy równoważenia obciążenia i dostępności platformy Azure
titleSuffix: Azure Load Balancer
description: Dzięki tej ścieżce szkoleniowej rozpocznij pracę z modułem równoważenia obciążenia standardowego platformy Azure i strefami dostępności.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197151"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Usługa Load Balancer w warstwie Standardowa i strefy dostępności

Azure Standard Load Balancer obsługuje [scenariusze stref dostępności.](../availability-zones/az-overview.md) Standardowego modułu równoważenia obciążenia można użyć do optymalizacji dostępności w scenariuszu end-to-end, dostosowując zasoby do stref i rozmieszczając je w różnych strefach.  Przejrzyj [strefy dostępności,](../availability-zones/az-overview.md) aby uzyskać wskazówki dotyczące stref dostępności, regionów, które obecnie obsługują strefy dostępności, oraz innych powiązanych pojęć i produktów. Strefy dostępności w połączeniu ze standardowym modułem równoważenia obciążenia to obszerny i elastyczny zestaw funkcji, który może tworzyć wiele różnych scenariuszy.  Przejrzyj ten dokument, aby zrozumieć te [pojęcia](#concepts) i podstawowe [wskazówki dotyczące projektowania scenariuszy](#design).

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>Pojęcia dotyczące stref dostępności zastosowane do modułu równoważenia obciążenia

Sam zasób modułu równoważenia obciążenia jest regionalny i nigdy nie jest strefowy. Ziarnistość tego, co można skonfigurować jest ograniczona przez każdą konfigurację definicji puli frontend, reguły i wewnętrznej bazy danych.
W kontekście stref dostępności zachowanie i właściwości reguły modułu równoważenia obciążenia są opisane jako strefy nadmiarowe lub strefowe.  Strefowo nadmiarowe i strefowe opisują strefowość właściwości.  W kontekście modułu równoważenia obciążenia strefa nadmiarowa zawsze oznacza *wiele stref,* a strefowe środki izolują usługę do *jednej strefy.*
Zarówno publiczne, jak i wewnętrzne moduły równoważenia obciążenia obsługują scenariusze strefowe i strefowe, a oba mogą kierować ruch między strefami w razie potrzeby *(równoważenie obciążenia między strefami).* 

### <a name="frontend"></a>Fronton

Frontend modułu równoważenia obciążenia to konfiguracja IP frontendu odwołująca się do zasobu publicznego adresu IP lub prywatnego adresu IP w podsieci zasobu sieci wirtualnej.  Tworzy punkt końcowy z równoważenia obciążenia, gdzie usługa jest narażona.
Zasób modułu równoważenia obciążenia może zawierać reguły z jednocześnie strefowymi i nadmiarowymi frontonami strefowymi. Gdy publiczny zasób IP lub prywatny adres IP został zagwarantowany w strefie, strefowość (lub jej brak) nie jest modyfikowalna.  Jeśli chcesz zmienić lub pominąć strefowość publicznej bazy ip lub prywatnego adresu IP, musisz odtworzyć publiczny adres IP w odpowiedniej strefie.  Strefy dostępności nie zmieniają ograniczeń dla wielu frontendów, przejrzyj [wiele frontendów dla modułu równoważenia obciążenia,](load-balancer-multivip-overview.md) aby uzyskać szczegółowe informacje na temat tej możliwości.

#### <a name="zone-redundant"></a>Strefa nadmiarowa 

W regionie ze strefami dostępności frontend standardowego modułu równoważenia obciążenia może być nadmiarowy.  Nadmiar strefy oznacza, że wszystkie przepływy przychodzące lub wychodzące są obsługiwane przez wiele stref dostępności w regionie jednocześnie przy użyciu jednego adresu IP. Schematy nadmiarowości DNS nie są wymagane. Adres IP pojedynczego frontona może przetrwać awarię strefy i może służyć do dotarcia do wszystkich (nie dotyczy) członków puli wewnętrznej bazy danych, niezależnie od strefy. Co najmniej jedna strefa dostępności może zakończyć się niepowodzeniem, a ścieżka danych przetrwa tak długo, jak jedna strefa w regionie pozostaje w dobrej kondycji. Pojedynczy adres IP frontendu jest obsługiwany jednocześnie przez wiele niezależnych wdrożeń infrastruktury w wielu strefach dostępności.  Nie oznacza to, że ścieżka danych bez trafień, ale wszelkie ponownych prób lub ponownego nawiązania zakończy się powodzeniem w innych strefach, na które nie ma wpływu błąd strefy.   

#### <a name="optional-zone-isolation"></a>Opcjonalna izolacja strefy

Można wybrać, aby fronton był gwarantowany dla pojedynczej strefy, która jest znana jako *fronton strefowy.*  Oznacza to, że każdy przepływ przychodzący lub wychodzący jest obsługiwany przez jedną strefę w regionie.  Twój frontend dzieli los ze zdrowiem strefy.  Ścieżka danych nie ma wpływu na błędy w strefach innych niż gdzie została zagwarantowana. Za pomocą strefowych frontonów można uwidaczniać adres IP na strefę dostępności.  

Ponadto można zużywać strefowe frontendy bezpośrednio dla punktów końcowych z równoważenia obciążenia w każdej strefie. Można również użyć tej funkcji, aby udostępnić punkty końcowe z równoważenia obciążenia na strefę, aby indywidualnie monitorować każdą strefę.  W przypadku publicznych punktów końcowych można je zintegrować z produktem równoważenia obciążenia DNS, takim jak [Traffic Manager,](../traffic-manager/traffic-manager-overview.md) i użyć jednej nazwy DNS. Następnie klient rozpozna tę nazwę DNS na wiele strefowych adresów IP.  

Jeśli chcesz połączyć te pojęcia (strefowo nadmiarowe i strefowe dla tego samego zaplecza), przejrzyj [wiele frontonów dla modułu Azure Load Balancer.](load-balancer-multivip-overview.md)

W przypadku publicznego frontendu modułu równoważenia obciążenia należy dodać parametr *stref* do publicznego zasobu IP, do którego odwołuje się konfiguracja IP wewnętrznej bazy danych używana przez odpowiednią regułę.

For an internal Load Balancer frontend, add a *zones* parameter to the internal Load Balancer frontend IP configuration. Wewnętrznej linii strefowej powoduje, że moduł równoważenia obciążenia gwarantuje adres IP w podsieci do określonej strefy.

### <a name="cross-zone-load-balancing"></a>Równoważenie obciążenia między strefami

Równoważenie obciążenia między strefami jest zdolność modułu równoważenia obciążenia do osiągnięcia punktu końcowego wewnętrznej bazy danych w dowolnej strefie i jest niezależna od frontonu i jego strefowości.  Każda reguła równoważenia obciążenia może kierować wystąpienie wewnętrznej bazy danych w dowolnej strefie dostępności lub wystąpieniach regionalnych.

Należy zadbać o skonstruowanie scenariusza w sposób, który wyraził pojęcie stref dostępności. Na przykład należy zagwarantować wdrożenie maszyny wirtualnej w jednej strefie lub wielu strefach i wyrównać strefowe zasoby frontonu i strefowego zaplecza do tej samej strefy.  Jeśli przecinasz strefy dostępności z tylko zasobami strefowymi, scenariusz będzie działać, ale może nie mieć przejrzystego trybu awarii w odniesieniu do stref dostępności. 

### <a name="backend"></a>Zaplecze

Moduł równoważenia obciążenia współpracuje z wystąpieniami maszyn wirtualnych.  Mogą to być zestawy autonomiczne, zestawy dostępności lub zestawy skalowania maszyny wirtualnej.  Każde wystąpienie maszyny wirtualnej w jednej sieci wirtualnej może być częścią puli wewnętrznej bazy danych, niezależnie od tego, czy została ona zagwarantowana w strefie lub która strefa została zagwarantowana.

Jeśli chcesz wyrównać i zagwarantować frontend i wewnętrznej bazy danych z jednej strefy, tylko miejsce maszyn wirtualnych w tej samej strefie do odpowiedniej puli wewnętrznej bazy danych.

Jeśli chcesz adres maszyn wirtualnych w wielu strefach, po prostu miejsce maszyn wirtualnych z wielu stref w tej samej puli wewnętrznej bazy danych.  Korzystając z zestawów skalowania maszyny wirtualnej, można umieścić jeden lub więcej zestawów skalowania maszyny wirtualnej w tej samej puli wewnętrznej bazy danych.  Każdy z tych zestawów skalowania maszyny wirtualnej może znajdować się w jednej lub wielu strefach.

### <a name="outbound-connections"></a>Połączenia wychodzące

Te same właściwości strefowo nadmiarowe i strefowe mają zastosowanie do [połączeń wychodzących](load-balancer-outbound-connections.md).  Publiczny adres IP używany w przypadku połączeń wychodzących jest obsługiwany przez wszystkie strefy. Strefowy publiczny adres IP jest obsługiwany tylko przez strefę, w jakiej jest gwarantowana.  Alokacje portów SNAT połączenia wychodzącego przetrwać awarii strefy i scenariusz będzie nadal zapewniać wychodzące łączności SNAT, jeśli nie ma wpływu na błąd strefy.  Może to wymagać ponownego nawiązania transmisji lub ponownego nawiązania połączeń dla scenariuszy nadmiarowych stref, jeśli przepływ był obsługiwany przez strefę, do których doszło.  Nie ma to wpływu na przepływy w strefach innych niż strefy, których dotyczy problem.

Algorytm alokacji wstępnej portu SNAT jest taki sam z lub bez stref dostępności.

### <a name="health-probes"></a>Sondy kondycji

Istniejące definicje sondy kondycji pozostają tak, jak są bez stref dostępności.  Jednak rozszerzyliśmy model kondycji na poziomie infrastruktury. 

Korzystając z nadmiarowych frontonów strefowych, moduł równoważenia obciążenia rozszerza swój wewnętrzny model kondycji, aby niezależnie sondować osiągalność maszyny wirtualnej z każdej strefy dostępności i zamykać ścieżki między strefami, które mogły zakończyć się niepowodzeniem bez interwencji klienta.  Jeśli dana ścieżka nie jest dostępna z infrastruktury modułu równoważenia obciążenia jednej strefy do maszyny wirtualnej w innej strefie, moduł równoważenia obciążenia może wykryć i uniknąć tego błędu. Inne strefy, które mogą osiągnąć tę maszynę wirtualną, mogą nadal obsługiwać maszynę wirtualną z odpowiednich frontendów.  W rezultacie jest możliwe, że podczas zdarzeń awarii każda strefa może mieć nieco inne rozkłady nowych przepływów, chroniąc jednocześnie ogólną kondycję usługi end-to-end.

## <a name="design-considerations"></a><a name="design"></a>Zagadnienia projektowe

Moduł równoważenia obciążenia jest celowo elastyczny w kontekście stref dostępności. Można wyrównać do stref lub wybrać opcję nadmiaru stref dla każdej reguły.  Zwiększona dostępność może być w cenie zwiększonej złożoności i należy zaprojektować dostępność dla optymalnej wydajności.  Przyjrzyjmy się kilku ważnym zagadnieniom projektowym.

### <a name="automatic-zone-redundancy"></a>Automatyczna nadmiarowość stref

Moduł równoważenia obciążenia ułatwia pojedyncze ip jako nadmiarowy frontend strefy. Nadmiarowy adres IP strefy może bezpiecznie obsługiwać zasób strefowy w dowolnej strefie i może przetrwać co najmniej jeden błąd strefy, o ile jedna strefa pozostaje w dobrej kondycji w regionie. Z drugiej strony, strefowa frontend to zmniejszenie usługi do jednej strefy i dzieli los z odpowiednią strefą.

Nadmiarowość stref nie oznacza hitless datapath lub płaszczyzny sterowania;  jest to wyraźnie płaszczyzna danych. Przepływy nadmiarowe stref mogą używać dowolnych stref, a przepływy klienta będą używać wszystkich stref w dobrej kondycji w regionie. W przypadku awarii strefy przepływy ruchu przy użyciu stref w dobrej kondycji w tym momencie nie mają wpływu.  Przepływy ruchu przy użyciu strefy w czasie awarii strefy może mieć wpływ, ale aplikacje można odzyskać. Przepływy te mogą być kontynuowane w pozostałych strefach w dobrej kondycji w regionie po retransmisji lub ponownego nawiązania, gdy platforma Azure zbiegnie się wokół awarii strefy.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>Granice między strefami

Ważne jest, aby zrozumieć, że za każdym razem, gdy usługa end-to-end przecina strefy, dzielisz los nie z jedną strefą, ale potencjalnie wieloma strefami.  W rezultacie usługa end-to-end może nie uzyskać żadnej dostępności w przypadku wdrożeń nieobjętych strefami.

Należy unikać wprowadzania niezamierzonych zależności między strefami, co zniweczy wzrost dostępności podczas korzystania ze stref dostępności.  Gdy aplikacja składa się z wielu składników i chcesz być odporny na awarię strefy, należy zadbać o zapewnienie przetrwania wystarczającej ilości krytycznych składników w przypadku awarii strefy.  Na przykład pojedynczy składnik krytyczny dla aplikacji może mieć wpływ na całą aplikację, jeśli istnieje tylko w strefie innej niż strefy pozostałe.  Ponadto należy również wziąć pod uwagę przywracanie strefy i jak aplikacja będzie zbieżna. Musisz zrozumieć, jak przyczyny aplikacji w odniesieniu do awarii jego części. Przejrzyjmy kilka kluczowych punktów i wykorzystajmy je jako inspirację do pytań, jak myślisz o konkretnym scenariuszu.

- Jeśli aplikacja ma dwa składniki, takie jak adres IP i maszyna wirtualna z dyskiem zarządzanym i są one gwarantowane w strefie 1 i strefie 2, gdy strefa 1 ulegnie awarii, usługa end-to-end nie przetrwa, gdy strefa 1 ulegnie awarii.  Nie przekraczaj stref ze scenariuszami strefowymi, chyba że w pełni zrozumiesz, że tworzysz potencjalnie niebezpieczny tryb awarii.  Ten scenariusz może zapewnić elastyczność.

- Jeśli aplikacja ma dwa składniki, takie jak adres IP i maszyna wirtualna z dyskiem zarządzanym i są one gwarantowane jako strefy nadmiarowej i strefy 1 odpowiednio, usługa end-to-end przetrwa awarii strefy 2, strefy 3 lub obu, chyba że strefa 1 nie powiodło się.  Jednak tracisz pewną zdolność do rozumu o kondycji usługi, jeśli wszystko, co obserwujesz jest osiągalność frontendu.  Rozważ opracowanie bardziej rozbudowanego modelu kondycji i pojemności.  Można użyć stref nadmiarowych i strefowych pojęć razem, aby rozwinąć wgląd i łatwość zarządzania.

- Jeśli aplikacja ma dwa składniki, takie jak strefowo nadmiarowy moduł równoważenia obciążenia i krzyżowa skala maszyny wirtualnej ustawiona w trzech strefach, zasoby w strefach, na które awaria nie ma wpływu, będą dostępne, ale pojemność usługi end-to-end może ulec pogorszeniu podczas awarii strefy. Z punktu widzenia infrastruktury wdrożenie może przetrwać co najmniej jeden błąd strefy, a to rodzi następujące pytania:
  - Czy rozumiesz, jak przyczyny aplikacji o takich błędach i pojemność zdegradowana?
  - Czy w razie potrzeby musisz mieć zabezpieczenia w usłudze, aby wymusić przejście awaryjne do pary regionów?
  - Jak będzie monitorować, wykrywać i łagodzić taki scenariusz? Można użyć standardowej diagnostyki równoważenia obciążenia, aby zwiększyć monitorowanie wydajności usługi end-to-end. Zastanów się, co jest dostępne i co może wymagać rozszerzenia, aby uzyskać pełny obraz.

- Strefy mogą sprawić, że awarie będą łatwiejsze do zrozumienia i ograniczone.  Jednak błąd strefy nie różni się od innych błędów, jeśli chodzi o pojęcia, takie jak limity czasu, ponownych prób i algorytmów wycofywania. Mimo że moduł równoważenia obciążenia platformy Azure udostępnia ścieżki nadmiarowe w strefie i próbuje szybko odzyskać, na poziomie pakietu w czasie rzeczywistym może wystąpić retransmisja lub ponowne nałożenie podczas wystąpienia awarii i ważne jest, aby zrozumieć, jak aplikacja radzi sobie z awariami. Twój schemat równoważenia obciążenia przetrwa, ale musisz zaplanować następujące elementy:
  - Gdy strefa ulegnie awarii, czy usługa end-to-end rozumie to i jeśli stan zostanie utracony, w jaki sposób odzyskasz?
  - Po powrocie strefy, czy aplikacja rozumie, jak bezpiecznie zbiegać?

Przejrzyj [wzorce projektowania chmury platformy Azure,](https://docs.microsoft.com/azure/architecture/patterns/) aby zwiększyć odporność aplikacji na scenariusze awarii.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [strefach dostępności](../availability-zones/az-overview.md)
- Dowiedz się więcej o [standardowym równoważącym obciążenia](load-balancer-standard-overview.md)
- Dowiedz się, jak [równoważyć maszyny wirtualne w strefie przy użyciu standardowego modułu równoważenia obciążenia z strefowym frontonem](load-balancer-standard-public-zonal-cli.md)
- Dowiedz się, jak [równoważyć maszyny wirtualne w różnych strefach przy użyciu standardowego modułu równoważenia obciążenia z nadmiarowym frontendem strefowym](load-balancer-standard-public-zone-redundant-cli.md)
- Dowiedz się więcej o [wzorcach projektowania chmury platformy Azure,](https://docs.microsoft.com/azure/architecture/patterns/) aby zwiększyć odporność aplikacji na scenariusze awarii.
