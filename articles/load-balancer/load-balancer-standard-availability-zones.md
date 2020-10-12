---
title: Azure usługa Load Balancer w warstwie Standardowa i Strefy dostępności
titleSuffix: Azure Load Balancer
description: Korzystając z tej ścieżki szkoleniowej, Rozpocznij pracę z usługą Azure usługa Load Balancer w warstwie Standardowa i Strefy dostępności.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 541aa7da3e804931c1793e455bcbfca83c809dae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89669189"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Usługa Load Balancer w warstwie Standardowa i strefy dostępności

Usługa Azure usługa Load Balancer w warstwie Standardowa obsługuje scenariusze stref dostępności. Możesz użyć standardowego modułu równoważenia obciążenia, aby zwiększyć dostępność w całym scenariuszu przez wyrównanie zasobów do i dystrybucja między strefami. Strefy dostępności w połączeniu z usługą równoważenia obciążenia w warstwie Standardowa są rozległych i elastycznym zestawem funkcji, który może tworzyć wiele różnych scenariuszy.  Zapoznaj się z tym dokumentem, aby poznać te [koncepcje](#concepts) i podstawowe [wskazówki dotyczące projektowania](#design).

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Koncepcje Strefy dostępności zastosowane do Load Balancer

Moduł równoważenia obciążenia dziedziczy konfigurację strefy z jej składników: 

* Fronton
* Reguły
* Definicja puli zaplecza

W kontekście stref dostępności zachowanie i właściwości reguły modułu równoważenia obciążenia są opisane jako strefa nadmiarowa lub strefa.  W kontekście modułu równoważenia obciążenia strefa nadmiarowa zawsze oznacza, że **wiele stref** i stref oznacza izolowanie usługi do **pojedynczej strefy**. Azure Load Balancer ma dwa typy, publiczne i wewnętrzne. Oba typy modułów równoważenia obciążenia obsługują nadmiarowość stref i wdrożenie strefowe.  Oba typy modułów równoważenia obciążenia mogą kierować ruchem między strefami w razie konieczności.

## <a name="frontend"></a>Fronton

Fronton usługi równoważenia obciążenia to konfiguracja adresu IP frontonu, odwołująca się do publicznego adresu IP lub prywatnego adresu IP w podsieci sieci wirtualnej. Tworzy punkt końcowy ze zrównoważonym obciążeniem, w którym jest udostępniana usługa.

Zasób modułu równoważenia obciążenia może zawierać reguły o strefach i strefach nadmiarowych jednocześnie. Gdy publiczny lub prywatny adres IP jest gwarantowany dla strefy, zonality (lub jej brak) nie można zmienić. Aby zmienić lub pominąć zonality publicznego lub prywatnego adresu IP frontonu, należy ponownie utworzyć adres IP w odpowiedniej strefie. 

Strefy dostępności nie zmieniają ograniczeń dla wielu frontonów. Aby uzyskać szczegółowe informacje na temat tej możliwości, przejrzyj [wiele frontonów dla Load Balancer](load-balancer-multivip-overview.md) .

### <a name="zone-redundant"></a>Strefa nadmiarowa 

W regionie ze strefami dostępności Standardowa usługa równoważenia obciążenia może być strefowo nadmiarowa. Wiele stref może obsłużyć ruch przychodzący lub wychodzący w regionie. Ten ruch jest obsługiwany przez pojedynczy adres IP. Schematy nadmiarowości DNS nie są wymagane. 

Pojedynczy adres IP frontonu będzie przeżyje awarię strefy. Adres IP frontonu może być używany w celu uzyskania dostępu do wszystkich (bez wpływu) członków puli zaplecza, niezależnie od strefy. Co najmniej jedna strefa dostępności może zakończyć się niepowodzeniem, a ścieżka danych przeżyje, dopóki jedna strefa w regionie pozostanie w dobrej kondycji. 

Adres IP frontonu jest obsługiwany jednocześnie przez wiele niezależnych wdrożeń infrastruktury w wielu strefach dostępności. Wszelkie ponowne próby lub ponowna próba ponownego ustanowienia zakończą się pomyślnie w innych strefach, których nie dotyczy awaria strefy. 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network translator adresów sieciowych">
</p>

*Ilustracja: moduł równoważenia obciążenia strefowo nadmiarowy*

### <a name="zonal"></a>Strefowych

Można zdecydować, aby fronton miał zagwarantowaną pojedynczą strefę, zwaną *strefą frontonu*.  Ten scenariusz oznacza, że wszystkie przepływy ruchu przychodzącego lub wychodzącego są obsługiwane przez jedną strefę w regionie.  Fronton udostępnia losy z kondycją strefy.  Błędy w ścieżce danych nie mają wpływ na awarie w strefach innych niż to, w którym zostało zagwarantowane. Za pomocą stref frontonów można uwidocznić adres IP dla strefy dostępności.  

Ponadto jest obsługiwane korzystanie z stref frontonów bezpośrednio dla punktów końcowych ze zrównoważonym obciążeniem w ramach każdej strefy. Ta konfiguracja pozwala udostępnić punkty końcowe ze zrównoważonym obciążeniem strefy, aby osobno monitorować każdą strefę. W przypadku publicznych punktów końcowych można zintegrować je z produktem równoważenia obciążenia DNS, takim jak [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , i użyć pojedynczej nazwy DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network translator adresów sieciowych">
</p>

*Ilustracja: moduł równoważenia obciążenia w Zona*

Jeśli chcesz mieszać te koncepcje (strefowo nadmiarowe i zona dla tego samego zaplecza), przejrzyj [wiele frontonów dla Azure Load Balancer](load-balancer-multivip-overview.md).

W przypadku publicznego frontonu modułu równoważenia obciążenia należy dodać parametr **strefy** do publicznego adresu IP. Ten publiczny adres IP jest przywoływany przez konfigurację adresu IP frontonu używaną przez odpowiednią regułę.

W przypadku frontonu wewnętrznego modułu równoważenia obciążenia Dodaj parametr **strefy** do konfiguracji adresu IP frontonu modułu równoważenia obciążenia. Strefa frontonu gwarantuje adres IP w podsieci do określonej strefy.

## <a name="backend"></a>Zaplecze

Azure Load Balancer współpracuje z wystąpieniami maszyn wirtualnych. Wystąpienia te mogą być autonomiczne, zestawy dostępności lub zestawy skalowania maszyn wirtualnych. Każda maszyna wirtualna w jednej sieci wirtualnej może być częścią puli zaplecza, niezależnie od strefy, do której zagwarantowana jest maszyna wirtualna.

Aby wyrównać i zagwarantowanie frontonu i zaplecza przy użyciu jednej strefy, należy umieścić maszyny wirtualne w tej samej strefie w odpowiedniej puli zaplecza.

Aby zająć się maszynami wirtualnymi w wielu strefach, należy umieścić maszyny wirtualne z wielu stref w tej samej puli zaplecza. 

W przypadku korzystania z zestawów skalowania maszyn wirtualnych należy umieścić jeden lub więcej zestawów skalowania maszyn wirtualnych w tej samej puli zaplecza. Zestawy skalowania mogą znajdować się w jednej lub wielu strefach.

## <a name="outbound-connections"></a>Połączenia wychodzące

Strefy nadmiarowe i zona mają zastosowanie do [połączeń wychodzących](load-balancer-outbound-connections.md). Strefa nadmiarowy publiczny adres IP używany do połączeń wychodzących jest obsługiwany przez wszystkie strefy. Publiczny adres IP strefy jest obsługiwany tylko przez strefę, w której jest gwarantowana. 

Alokacje portów dla przydziałów ruchu wychodzącego nie zostały naruszone w przypadku awarii strefy. Awarie stref mogą wymagać ponownego ustanowienia połączeń dla scenariuszy nadmiarowych strefy, jeśli ruch został obsłużony przez strefę, której to dotyczy. Nie ma to wpływu na przepływy w strefach innych niż strefy, których to dotyczy.

Algorytm wstępnego przydzielenia portu dla translatora adresów sieciowych jest taki sam ze strefami dostępności lub bez.

## <a name="health-probes"></a>Sondy kondycji

Istniejące definicje sondy kondycji pozostają w stanie, w jakim się znajdują, bez stref dostępności. Jednak model kondycji został rozszerzony na poziomie infrastruktury. 

W przypadku korzystania ze strefowo nadmiarowych frontonów moduł równoważenia obciążenia rozszerza swoje wewnętrzne Sprawdzanie kondycji. Moduł równoważenia obciążenia niezależnie sprawdza dostępność maszyny wirtualnej z każdej strefy i zamyka ścieżki między strefami zakończonymi niepowodzeniem bez interwencji.  

Inne strefy, które mogą nawiązać połączenie z tą maszyną wirtualną, mogą nadal obsłużyć maszynę wirtualną z odpowiednich frontonów. W przypadku wystąpienia błędów każda strefa może mieć różne dystrybucje nowych przepływów przy jednoczesnej ochronie ogólnej kondycji usługi.

## <a name="design-considerations"></a><a name="design"></a> Zagadnienia dotyczące projektowania

Usługa równoważenia obciążenia jest elastyczna w kontekście stref dostępności. Dla każdej reguły można wybrać opcję Wyrównaj do stref lub być nadmiarowe strefy. Zwiększona dostępność może być naliczana w cenie zwiększonej złożoności. Zaprojektuj dostępność w celu uzyskania optymalnej wydajności.

### <a name="zone-redundancy"></a>Strefa — nadmiarowość

Load Balancer ułatwia używanie pojedynczego adresu IP jako frontonu Strefowo nadmiarowego. Strefowo nadmiarowy adres IP może obsłużyć zasób strefy w dowolnej strefie.  Adres IP może przetrwać co najmniej jedną awarię strefy, o ile jedna strefa pozostaje w dobrej kondycji w regionie.  Zamiast tego strefa frontonu jest redukcją usługi do pojedynczej strefy i udostępnia losy z odpowiednią strefą.

Nadmiarowość strefy nie oznacza hitless datapath lub płaszczyzny kontrolnej; jest to płaszczyzna danych. Przepływ nadmiarowy strefy może korzystać ze wszystkich stref, a przepływy klientów będą używać wszystkich stref w dobrej kondycji w regionie. W przypadku awarii strefy nie ma to wpływu na przepływy ruchu korzystające z prawidłowych stref. 

Może to wpływać na przepływy ruchu korzystające z strefy w czasie awarii strefy, ale aplikacje mogą je odzyskać. Ruch kontynuuje się w strefach zdrowych w regionie podczas ponownej transmisji, gdy platforma Azure połączyła się z awarią strefy.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Granice między strefami

Ważne jest, aby zrozumieć, że kiedykolwiek usługa przekroczy strefy, możesz udostępniać losy nie tylko jednej strefie, ale potencjalnie w wielu strefach. W związku z tym usługa mogła nie uzyskać dostępności w ramach wdrożeń nienależących do stref.

Unikaj wprowadzania niezamierzonych zależności między strefami, co spowoduje zniesienia korzyści z dostępności podczas korzystania ze stref dostępności. Jeśli aplikacja zawiera wiele składników krytycznych, należy się upewnić, że w przypadku awarii strefy wystąpi awaria.

Pojedynczy składnik krytyczny dla aplikacji może mieć wpływ na całą aplikację, jeśli istnieje tylko w strefie innej niż w przypadku pozostałych stref. Rozważ przywrócenie strefy i sposób, w jaki aplikacja będzie zbieżna. Dowiedz się, w jaki sposób aplikacja reaguje na niepowodzenia części IT. Zapoznaj się z kluczowymi punktami i korzystaj z nich w celu uzyskania odpowiedzi na pytania w konkretnym scenariuszu.

- Jeśli aplikacja ma dwa składniki:

    * Adres IP
    * Maszyna wirtualna z dyskiem zarządzanym

Są one konfigurowane w strefach 1 i 2. Gdy strefa 1 ulegnie awarii, usługa nie będzie działać. Nie należy przechodzić między strefami ze scenariuszami stref, chyba że w pełni rozumiesz, że tworzysz bezpieczny tryb awarii. Ten scenariusz może zapewnić elastyczność.

- Jeśli aplikacja ma dwa składniki:

    * Adres IP
    * Maszyna wirtualna z dyskiem zarządzanym

Są one skonfigurowane jako strefy nadmiarowe i Strefa 1. Usługa będzie przetrwać awarię strefy 2, strefy 3 lub obie, chyba że strefa 1 nie powiodła się. Jednak utracisz pewne możliwości związane z kondycją usługi, jeśli wszystko, co jest zauważalne, jest osiągalne dla frontonu.  Zapoznaj się z bardziej rozbudowanym modelem kondycji i pojemności.  W celu zwiększenia wglądu w dane i łatwość zarządzania mogą być używane koncepcje nadmiarowe stref i strefowo.

- Jeśli aplikacja ma dwa składniki:

    * Fronton strefy nadmiarowego modułu równoważenia obciążenia
    * Wielostrefowy zestaw skalowania maszyn wirtualnych w trzech strefach

Zasoby w strefach, których nie dotyczy awaria, będą dostępne. Wydajność usługi może być obniżona podczas awarii. Z punktu widzenia infrastruktury wdrożenie może przetrwać co najmniej jedną awarię strefy. W tym scenariuszu przedstawiono następujące pytania:

  - Czy rozumiesz, w jaki sposób aplikacja reaguje na awarie i obniża wydajność?
  - Czy musisz mieć zabezpieczenia w usłudze, aby wymusić przejście w tryb failover do pary regionów w razie potrzeby?
  - Jak będzie można monitorować, wykrywać i ograniczać ten scenariusz? Możesz użyć standardowej diagnostyki modułu równoważenia obciążenia, aby rozszerzyć monitorowanie wydajności usługi. Zastanów się, co jest dostępne i co może wymagać rozszerzenia.

- Strefy mogą sprawiać, że błędy są łatwiejsze do zrozumienia i zawarte. Awaria strefy nie różni się od innych błędów, gdy występuje ona w przypadku przekroczenia limitu czasu, ponawiania prób i algorytmów wycofywania. Azure Load Balancer zapewnia ścieżki nadmiarowe strefy. Moduł równoważenia obciążenia próbuje szybko wykonać odzyskiwanie na poziomie pakietu w czasie rzeczywistym. Retransmisje lub remisje mogą wystąpić podczas wystąpienia awarii. Ważne jest, aby zrozumieć, w jaki sposób aplikacja będzie rozwiązywać błędy. Schemat równoważenia obciążenia będzie przeżyje, ale należy zaplanować następujące pytania:

  - W przypadku niepowodzenia strefy usługa zrozumie ten błąd i jeśli stan zostanie utracony, w jaki sposób zostanie odzyskany?
  - Czy w przypadku powracania strefy aplikacja rozumie, jak bezpiecznie przeprowadzić zbieżność?

Przejrzyj [wzorce projektowania w chmurze platformy Azure](https://docs.microsoft.com/azure/architecture/patterns/) , aby zwiększyć odporność aplikacji na błędy.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [strefy dostępności](../availability-zones/az-overview.md)
- Dowiedz się więcej o [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)
- Dowiedz się, jak [równoważyć obciążenie maszyn wirtualnych w strefie przy użyciu usługa Load Balancer w warstwie Standardowa z strefą frontonu](load-balancer-standard-public-zonal-cli.md)
- Dowiedz się, jak [równoważyć obciążenie maszyn wirtualnych między strefami przy użyciu usługa Load Balancer w warstwie Standardowa ze strefowo nadmiarowy fronton](load-balancer-standard-public-zone-redundant-cli.md)
- Dowiedz się więcej o [wzorcach projektowych chmury platformy Azure](https://docs.microsoft.com/azure/architecture/patterns/) w celu zwiększenia odporności aplikacji na scenariusze błędów.
