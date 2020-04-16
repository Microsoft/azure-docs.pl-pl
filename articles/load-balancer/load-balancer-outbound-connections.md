---
title: Połączenia wychodzące na platformie Azure
titleSuffix: Azure Load Balancer
description: W tym artykule wyjaśniono, jak platforma Azure umożliwia maszynom wirtualnym komunikowanie się z publicznymi usługami internetowymi.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: acf49c4247c8084a3afd3c2046003ee1b20d2f67
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393104"
---
# <a name="outbound-connections-in-azure"></a>Połączenia wychodzące na platformie Azure

Platforma Azure zapewnia łączność wychodzącą dla wdrożeń klientów za pośrednictwem kilku różnych mechanizmów. W tym artykule opisano, jakie są scenariusze, kiedy mają zastosowanie, jak działają i jak nimi zarządzać.

>[!NOTE] 
>W tym artykule omówiono tylko wdrożenia Menedżera zasobów. Przejrzyj [połączenia wychodzące (klasyczne)](load-balancer-outbound-connections-classic.md) dla wszystkich klasycznych scenariuszy wdrażania na platformie Azure.

Wdrożenie na platformie Azure może komunikować się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresowej IP. Gdy wystąpienie inicjuje przepływ wychodzący do miejsca docelowego w przestrzeni adresów adresów IP publicznych, platforma Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu tego mapowania ruch zwrotny dla tego wychodzącego przepływu pochodzi również dotrzeć do prywatnego adresu IP, z którego pochodzi przepływ.

Platforma Azure używa źródłowego translacji adresów sieciowych (SNAT) do wykonania tej funkcji. Gdy za jednym publicznym adresem IP podszywa się pod jeden publiczny adres IP, platforma Azure używa [tłumaczenia adresów portowych (PAT)](#pat) do maskowania prywatnych adresów IP. Porty efemeryczne są używane dla PAT i są [wstępnie przydzielane](#preallocatedports) na podstawie rozmiaru puli.

Istnieje wiele [scenariuszy wychodzących](#scenarios). W razie potrzeby można połączyć te scenariusze. Przejrzyj je dokładnie, aby zrozumieć możliwości, ograniczenia i wzorce, ponieważ mają one zastosowanie do modelu wdrażania i scenariusza aplikacji. Przejrzyj wskazówki dotyczące [zarządzania tymi scenariuszami](#snatexhaust).

>[!IMPORTANT] 
>Standardowy moduł równoważenia obciążenia i standardowy publiczny adres IP wprowadzają nowe możliwości i różne zachowania do łączności wychodzącej.  Nie są one takie same jak podstawowe jednostki SKU.  Jeśli chcesz łączności wychodzącej podczas pracy ze standardowymi jednostkami SKU, należy jawnie zdefiniować ją za pomocą standardowych publicznych adresów IP lub standardowego publicznego modułu równoważenia obciążenia.  Obejmuje to tworzenie łączności wychodzącej podczas korzystania z wewnętrznego standardowego modułu równoważenia obciążenia.  Zalecamy, aby zawsze używać reguł ruchu wychodzącego w standardowym publicznym bilansiowaniu obciążenia.  [Scenariusz 3](#defaultsnat) nie jest dostępny ze standardową jednostką SKU.  Oznacza to, że gdy używany jest wewnętrzny standardowy moduł równoważenia obciążenia, należy podjąć kroki w celu utworzenia łączności wychodzącej dla maszyn wirtualnych w puli wewnętrznej bazy danych, jeśli wymagana jest łączność wychodząca.  W kontekście łączności wychodzącej pojedynczej autonomicznej maszyny Wirtualnej, wszystkie maszyny wirtualne w zestawie dostępności, wszystkie wystąpienia w VMSS zachowują się jak grupa. Oznacza to, że jeśli pojedyncza maszyna wirtualna w zestawie dostępności jest skojarzona ze standardową jednostką SKU, wszystkie wystąpienia maszyn wirtualnych w ramach tego zestawu dostępności zachowują się teraz przez te same reguły, tak jak gdyby są skojarzone ze standardową jednostką SKU, nawet jeśli pojedyncze wystąpienie nie jest z nią bezpośrednio skojarzone. To zachowanie jest również obserwowane w przypadku autonomicznej maszyny Wirtualnej z wieloma kartami interfejsu sieciowego dołączonymi do modułu równoważenia obciążenia. Jeśli jedna karta sieciowa zostanie dodana jako autonomiczna, będzie miała takie samo zachowanie. Dokładnie przejrzyj cały ten dokument, aby zrozumieć ogólne pojęcia, przejrzyj [standardowy moduł równoważenia obciążenia](load-balancer-standard-overview.md) pod kątem różnic między jednostkami SKU i przejrzyj [reguły ruchu wychodzącego.](load-balancer-outbound-rules-overview.md)  Za pomocą reguł wychodzących umożliwia szczegółowe ziarniste kontroli nad wszystkimi aspektami łączności wychodzącej.

## <a name="scenario-overview"></a><a name="scenarios"></a>Omówienie scenariusza

Moduł równoważenia obciążenia platformy Azure i powiązane zasoby są jawnie definiowane podczas korzystania z [usługi Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)  Platforma Azure udostępnia obecnie trzy różne metody osiągania łączności wychodzącej dla zasobów usługi Azure Resource Manager. 

| Jednostki SKU | Scenariusz | Metoda | Protokoły IP | Opis |
| --- | --- | --- | --- | --- |
| Standard, Podstawowy | [1. Maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia (z modułem równoważenia obciążenia lub bez niego)](#ilpip) | SNAT, port maskarady nieużywanie | TCP, UDP, ICMP, ESP | Platforma Azure używa publicznego adresu IP przypisanego do konfiguracji IP karty sieciowej wystąpienia. Wystąpienie ma wszystkie porty efemeryczne dostępne. W przypadku korzystania ze standardowego modułu równoważenia obciążenia [reguły ruchu wychodzącego](load-balancer-outbound-rules-overview.md) nie są obsługiwane, jeśli publiczny adres IP jest przypisany do maszyny wirtualnej. |
| Standard, Podstawowy | [2. Publiczny moduł równoważenia obciążenia skojarzony z maszyną wirtualną (brak publicznego adresu IP w wystąpieniu)](#lb) | SNAT z maskaradą portową (PAT) z frontonami modułu równoważenia obciążenia | TCP, UDP |Platforma Azure udostępnia publiczny adres IP publicznych frontendów modułu równoważenia obciążenia z wieloma prywatnymi adresami IP. Platforma Azure używa tymczasowych portów frontendów do pat. Reguły [ruchu wychodzącego](load-balancer-outbound-rules-overview.md) należy używać do jawnego definiowania łączności wychodzącej. |
| brak lub podstawowe | [3. Samodzielna maszyna wirtualna (bez modułu równoważenia obciążenia, bez publicznego adresu IP)](#defaultsnat) | SNAT z maskaradą portów (PAT) | TCP, UDP | Platforma Azure automatycznie wyznacza publiczny adres IP dla usługi SNAT, udostępnia ten publiczny adres IP z wieloma prywatnymi adresami IP zestawu dostępności i używa tymczasowych portów tego publicznego adresu IP. Ten scenariusz jest rezerwowym dla poprzednich scenariuszy. Nie zalecamy go, jeśli potrzebujesz widoczności i kontroli. |

Jeśli nie chcesz, aby maszyna wirtualna komunikowała się z punktami końcowymi poza platformą Azure w przestrzeni adresów IP publicznych, możesz użyć sieciowych grup zabezpieczeń (NSG), aby zablokować dostęp w razie potrzeby. Sekcja [Zapobieganie łączności wychodzącej](#preventoutbound) omówiono szczegóły dotyczące sieciowych grup zabezpieczeń. Wskazówki dotyczące projektowania, implementowania i zarządzania siecią wirtualną bez dostępu wychodzącego nie są objęte zakresem tego artykułu.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scenariusz 1: Maszyna wirtualna z publicznym adresem IP

W tym scenariuszu maszyna wirtualna ma publiczny adres IP przypisany do niego. Jeśli chodzi o połączenia wychodzące, nie ma znaczenia, czy maszyna wirtualna jest równoważenia obciążenia, czy nie. Ten scenariusz ma pierwszeństwo przed innymi. Gdy publiczny adres IP jest używany, maszyna wirtualna używa publicznego adresu IP dla wszystkich przepływów wychodzących.  

Publiczny adres IP przypisany do maszyny Wirtualnej jest relacją 1:1 (a nie 1: wiele) i implementowany jako bezstanowy nat 1:1.  Maskowanie portów (PAT) nie jest używane, a maszyna wirtualna ma wszystkie porty efemeryczne dostępne do użycia.

Jeśli aplikacja inicjuje wiele przepływów wychodzących i występują wyczerpanie portów SNAT, należy rozważyć przypisanie publicznego adresu IP w [celu złagodzenia ograniczeń SNAT](#assignilpip). Przegląd [Zarządzanie wyczerpania SNAT](#snatexhaust) w całości.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scenariusz 2: Maszyna wirtualna z równoważenia obciążenia bez publicznego adresu IP

W tym scenariuszu maszyna wirtualna jest częścią puli wewnętrznej bazy danych modułu równoważenia obciążenia. Maszyna wirtualna nie ma przypisanego do niej publicznego adresu IP. Zasób modułu równoważenia obciążenia musi być skonfigurowany z regułą równoważenia obciążenia, aby utworzyć łącze między publicznym frontendem IP z pulą wewnętrznej bazy danych.

Jeśli ta konfiguracja reguły nie zostanie ukończona, zachowanie jest opisane w scenariuszu dla [autonomicznej maszyny Wirtualnej bez publicznego adresu IP.](#defaultsnat) Nie jest konieczne dla reguły, aby mieć działającego odbiornika w puli wewnętrznej bazy danych dla sondy kondycji, aby zakończyć się pomyślnie.

Gdy maszyna wirtualna z równoważenia obciążenia tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny źródłowy adres IP przepływu wychodzącego do publicznego adresu IP publicznego frontonatu modułu równoważenia obciążenia. Platforma Azure używa SNAT do wykonywania tej funkcji. Platforma Azure używa również [pat](#pat) do maskarada wielu prywatnych adresów IP za publiczny adres IP. 

Porty efemeryczne publicznego frontonu adresów IP modułu równoważenia obciążenia służą do rozróżniania poszczególnych przepływów pochodzących z maszyny Wirtualnej. SNAT dynamicznie używa [wstępnie przydzielonych portów tymczasowych](#preallocatedports) podczas tworzenia przepływów wychodzących. W tym kontekście porty efemeryczne używane dla SNAT są nazywane portami SNAT.

Porty SNAT są wstępnie przydzielane zgodnie z opisem w sekcji [Opis SNAT i PAT.](#snat) Są to ograniczone zasoby, które można wyczerpać. Ważne jest, aby zrozumieć, w jaki sposób są [one spożywane](#pat). Aby zrozumieć, jak zaprojektować dla tego zużycia i złagodzić w razie potrzeby, przegląd [zarządzania wyczerpania SNAT](#snatexhaust).

Jeśli [wiele publicznych adresów IP jest skojarzonych z modułem równoważenia obciążenia Basic,](load-balancer-multivip-overview.md)każdy z tych publicznych adresów IP jest kandydatem do przepływów wychodzących, a jeden jest wybierany losowo.  

Aby monitorować kondycję połączeń wychodzących za pomocą modułu równoważenia obciążenia Basic, można użyć [dzienników usługi Azure Monitor dla modułu równoważenia obciążenia](load-balancer-monitor-log.md) i [dzienników zdarzeń alertów](load-balancer-monitor-log.md#alert-event-log) do monitorowania komunikatów wyczerpania portów SNAT.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scenariusz 3: Autonomiczna maszyna wirtualna bez publicznego adresu IP

W tym scenariuszu maszyna wirtualna nie jest częścią puli publicznego modułu równoważenia obciążenia (a nie częścią wewnętrznej puli modułu równoważenia obciążenia standardowego) i nie ma przypisanego do niej publicznego adresu IP. Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny źródłowy adres IP przepływu wychodzącego na publiczny źródłowy adres IP. Publiczny adres IP używany dla tego przepływu wychodzącego nie jest konfigurowalny i nie jest wliczany do limitu publicznych zasobów IP subskrypcji. Ten publiczny adres IP nie należy do Ciebie i nie może być zarezerwowany. Jeśli ponownie wdrożysz zestaw skalowania maszyny wirtualnej lub zestawu dostępności lub maszyny wirtualnej, ten publiczny adres IP zostanie wydany i zażądany zostanie nowy publiczny adres IP. Nie należy używać tego scenariusza do umieszczania adresów IP na białej liście. Zamiast tego należy użyć jednego z dwóch pozostałych scenariuszy, w których jawnie zadeklarować scenariusz wychodzących i publiczny adres IP, które mają być używane dla łączności wychodzącej.

>[!IMPORTANT] 
>Ten scenariusz ma również zastosowanie, gdy jest dołączony __tylko__ wewnętrzny podstawowy moduł równoważenia obciążenia. Scenariusz 3 nie jest __dostępny,__ gdy do maszyny wirtualnej jest dołączony wewnętrzny standardowy moduł równoważenia obciążenia.  Oprócz używania wewnętrznego standardowego modułu równoważenia obciążenia należy jawnie utworzyć [scenariusz 1](#ilpip) lub [scenariusz 2.](#lb)

Platforma Azure używa narzędzia SNAT z maskaradą portów[(PAT)](#pat)do wykonania tej funkcji. Ten scenariusz jest podobny do [scenariusza 2](#lb), z tą różnicą, że nie ma kontroli nad używanym adresem IP. Jest to scenariusz rezerwowy, gdy scenariusze 1 i 2 nie istnieją. Nie zaleca się tego scenariusza, jeśli chcesz kontrolować adres wychodzący. Jeśli połączenia wychodzące są krytyczną częścią aplikacji, należy wybrać inny scenariusz.

Porty SNAT są wstępnie przydzielane zgodnie z opisem w sekcji [Opis SNAT i PAT.](#snat)  Liczba maszyn wirtualnych współużytkuje zestaw dostępności określa, która warstwa preallocation ma zastosowanie.  Autonomiczna maszyna wirtualna bez zestawu dostępności jest skutecznie puli 1 do celów określania preallocation (1024 portów SNAT). Porty SNAT są ograniczonym zasobem, który można wyczerpać. Ważne jest, aby zrozumieć, w jaki sposób są [one spożywane](#pat). Aby zrozumieć, jak zaprojektować dla tego zużycia i złagodzić w razie potrzeby, przegląd [zarządzania wyczerpania SNAT](#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Wiele połączonych scenariuszy

Można połączyć scenariusze opisane w poprzednich sekcjach, aby osiągnąć określony wynik. Gdy istnieje wiele scenariuszy, stosuje się kolejność pierwszeństwa: [scenariusz 1](#ilpip) ma pierwszeństwo przed [scenariuszem 2](#lb) i [3](#defaultsnat). [Scenariusz 2](#lb) zastępuje [scenariusz 3](#defaultsnat).

Przykładem jest wdrożenie usługi Azure Resource Manager, gdzie aplikacja opiera się w dużej mierze na połączeniach wychodzących do ograniczonej liczby miejsc docelowych, ale także odbiera przepływy przychodzące za pomocą frontonu modułu równoważenia obciążenia. W takim przypadku można połączyć scenariusze 1 i 2 dla reliefu. Aby uzyskać dodatkowe wzorce, przejrzyj [zarządzanie wyczerpaniem SNAT](#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Wiele frontendów przepływów wychodzących

#### <a name="standard-load-balancer"></a>Standardowy moduł równoważenia obciążenia

Standardowy moduł równoważenia obciążenia używa wszystkich kandydatów do przepływów wychodzących w tym samym czasie, gdy istnieje [wiele (publicznych) frontendów IP.](load-balancer-multivip-overview.md) Każdy fronton mnoży liczbę dostępnych portów SNAT z prew alokacji, jeśli reguła równoważenia obciążenia jest włączona dla połączeń wychodzących.

Można pominąć adres IP wewnętrznej bazy danych z używanego dla połączeń wychodzących z nową opcją reguły równoważenia obciążenia:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Zwykle `disableOutboundSnat` opcja domyślnie _false_ i oznacza, że ta reguła programów wychodzących SNAT dla skojarzonych maszyn wirtualnych w puli wewnętrznej bazy danych reguły równoważenia obciążenia. `disableOutboundSnat` Można zmienić na _true,_ aby zapobiec modułowi równoważenia obciążenia przy użyciu skojarzonego adresu IP frontendu dla połączeń wychodzących dla maszyn wirtualnych w puli wewnętrznej bazy danych tej reguły równoważenia obciążenia.  I nadal można wyznaczyć określony adres IP dla przepływów wychodzących, zgodnie z opisem w [wielu, połączone scenariusze,](#combinations) jak również.

#### <a name="load-balancer-basic"></a>Moduł równoważenia obciążenia Podstawowy

Load Balancer Basic wybiera pojedynczy frontend, który ma być używany dla przepływów wychodzących, gdy [wiele (publicznych) frontendów IP](load-balancer-multivip-overview.md) jest kandydatami do przepływów wychodzących. Ten wybór nie jest konfigurowalny i należy wziąć pod uwagę algorytm wyboru jako losowy. Można wyznaczyć określony adres IP dla przepływów wychodzących, zgodnie z opisem w [wielu połączonych scenariuszach](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Strefy dostępności

W przypadku korzystania [ze standardowego modułu równoważenia obciążenia ze strefami dostępności](load-balancer-standard-availability-zones.md)frontowe nadmiarowe strefy mogą zapewniać nadmiarowe połączenia SNAT wychodzące strefy, a programowanie SNAT przetrwa awarię strefy.  Gdy używane są frontony strefowe, wychodzące połączenia SNAT dzielą los ze strefą, do której należą.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Zrozumienie SNAT i PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Port maskarady SNAT (PAT)

Gdy zasób publicznego modułu równoważenia obciążenia jest skojarzony z wystąpieniami maszyn wirtualnych, każde źródło połączenia wychodzącego jest przepisywane. Źródło jest przepisane z prywatnej przestrzeni adresów IP w sieci wirtualnej do adresu IP publicznego frontonatu modułu równoważenia obciążenia. W przestrzeni publicznego adresu IP 5-krotka przepływu (źródłowy adres IP, port źródłowy, protokół transportu IP, docelowy adres IP, port docelowy) musi być unikatowa.  Port maskarady SNAT może być używany z protokołami TCP lub UDP IP.

Porty efemeryczne (porty SNAT) są używane do osiągnięcia tego celu po przepisaniu prywatnego źródłowego adresu IP, ponieważ wiele przepływów pochodzi z jednego publicznego adresu IP. Algorytm SNAT podszywający się pod port przydziela porty SNAT w różny sposób dla protokołu UDP i TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>Porty TCP SNAT

Jeden port SNAT jest zużywany na przepływ do jednego docelowego adresu IP, portu. W przypadku wielu przepływów TCP do tego samego docelowego adresu IP, portu i protokołu każdy przepływ TCP zużywa jeden port SNAT. Gwarantuje to, że przepływy są unikatowe, gdy pochodzą z tego samego publicznego adresu IP i przejść do tego samego docelowego adresu IP, portu i protokołu. 

Wiele przepływów, każdy do innego docelowego adresu IP, portu i protokołu, współużytkuje jeden port SNAT. Docelowy adres IP, port i protokół sprawiają, że przepływy są unikatowe bez konieczności stosowania dodatkowych portów źródłowych w celu odróżnienia przepływów w przestrzeni publicznego adresu IP.

#### <a name="udp-snat-ports"></a><a name="udp"></a>Porty SNAT UDP

Porty SNAT UDP są zarządzane przez inny algorytm niż porty TCP SNAT.  Load Balancer używa algorytmu znanego jako "port-restricted cone NAT" dla UDP.  Jeden port SNAT jest zużywany dla każdego przepływu, niezależnie od docelowego adresu IP, portu.

#### <a name="snat-port-reuse"></a>Ponowne użycie portu SNAT

Po zwolnieniu portu port jest dostępny do ponownego użycia w razie potrzeby.  Porty SNAT można myśleć jako sekwencji od najniższej do najwyższej dostępne dla danego scenariusza, a pierwszy dostępny port SNAT jest używany dla nowych połączeń. 
 
#### <a name="exhaustion"></a>Wyczerpania

Gdy zasoby portu SNAT są wyczerpane, przepływy wychodzące nie powiodą się, dopóki istniejące przepływy nie zwolnią portów SNAT. Moduł równoważenia obciążenia odzyskuje porty SNAT po zamknięciu przepływu i używa [4-minutowego limitu czasu bezczynnego](#idletimeout) do odzyskiwania portów SNAT z przepływów bezczynnych.

Porty UDP SNAT zazwyczaj wyczerpuje się znacznie szybciej niż porty TCP SNAT ze względu na różnicę w algorytmie. Musisz zaprojektować i skalować test z tą różnicą na uwadze.

W przypadku wzorców w celu złagodzenia warunków, które często prowadzą do wyczerpania portów SNAT, przejrzyj sekcję [Zarządzanie SNAT.](#snatexhaust)

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Preallocation portu efemerycznego dla portu maskarady SNAT (PAT)

Platforma Azure używa algorytmu do określenia liczby wstępnie przydzielonych portów SNAT dostępnych na podstawie rozmiaru puli zaplecza podczas korzystania z portu maskującego SNAT ([PAT](#pat)). Porty SNAT są portami tymczasowymi dostępnymi dla określonego publicznego adresu źródłowego IP.

Ta sama liczba portów SNAT jest wstępnie przydzielona odpowiednio dla protokołu UDP i TCP i zużywana niezależnie na protokół transportu IP.  Jednak użycie portu SNAT różni się w zależności od tego, czy przepływ jest UDP lub TCP.

>[!IMPORTANT]
>Standardowe programowanie SNAT SKU jest na protokół transportu IP i pochodzi z reguły równoważenia obciążenia.  Jeśli istnieje tylko reguła równoważenia obciążenia TCP, SNAT jest dostępny tylko dla protokołu TCP. Jeśli masz tylko regułę równoważenia obciążenia TCP i potrzebujesz wychodzącego SNAT dla protokołu UDP, utwórz regułę równoważenia obciążenia UDP z tego samego frontendu do tej samej puli wewnętrznej bazy danych.  Spowoduje to uruchomienie programowania SNAT dla UDP.  Reguła robocza lub sonda kondycji nie jest wymagana.  Podstawowa jednostka SKU SNAT zawsze programuje protokół SNAT dla obu protokołów transportu IP, niezależnie od protokołu transportu określonego w regule równoważenia obciążenia.

Platforma Azure wstępnie przydziela porty SNAT do konfiguracji IP karty sieciowej każdej maszyny Wirtualnej. Po dodaniu konfiguracji IP do puli porty SNAT są wstępnie przydzielane dla tej konfiguracji ip na podstawie rozmiaru puli wewnętrznej bazy danych. Podczas tworzenia przepływów wychodzących [pat](#pat) dynamicznie zużywa (do wstępnie przydzielonego limitu) i zwalnia te porty po zamknięciu przepływu lub [wystąpieniu bezczynności](#idletimeout) limitu czasu.

W poniższej tabeli przedstawiono wstępne alokacje portów SNAT dla warstw rozmiarów puli zaplecza:

| Rozmiar puli (wystąpienia maszyn wirtualnych) | Wstępnie przydzielone porty SNAT na konfigurację IP|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> W przypadku korzystania ze standardowego modułu równoważenia obciążenia z [wieloma frontonami](load-balancer-multivip-overview.md)każdy adres IP wewnętrznej bazy danych mnoży liczbę dostępnych portów SNAT w poprzedniej tabeli. Na przykład pula wewnętrznej bazy danych 50 maszyn wirtualnych z 2 regułami równoważenia obciążenia, każda z oddzielnym adresem IP wewnętrznej bazy danych, użyje portów SNAT 2048 (2x 1024) na regułę. Zobacz szczegóły dotyczące [wielu frontendów](#multife).

Należy pamiętać, że liczba dostępnych portów SNAT nie przekłada się bezpośrednio na liczbę przepływów. Jeden port SNAT może być ponownie odtwarzany w wielu unikatowych miejscach docelowych. Porty są używane tylko wtedy, gdy jest to konieczne, aby przepływy unikatowe. Aby uzyskać wskazówki dotyczące projektowania i łagodzenia, zapoznaj się z sekcją dotyczącą [zarządzania tym zasobem wyczerpującym](#snatexhaust) oraz sekcją opisującą [PAT](#pat).

Zmiana rozmiaru puli wewnętrznej bazy danych może mieć wpływ na niektóre z ustalonych przepływów. Jeśli rozmiar puli wewnętrznej bazy danych zwiększa się i przechodzi do następnej warstwy, połowa wstępnie przydzielonych portów SNAT są odzyskiwać podczas przejścia do następnej większej warstwy puli wewnętrznej bazy. Przepływy, które są skojarzone z odzyskanego portu SNAT limit czasu i muszą zostać przywrócone. Jeśli zostanie podjęta próba nowego przepływu, przepływ zakończy się natychmiast tak długo, jak porty wstępnie przydzielone są dostępne.

Jeśli rozmiar puli wewnętrznej bazy danych zmniejsza się i przechodzi do niższej warstwy, zwiększa się liczba dostępnych portów SNAT. W takim przypadku nie ma to wpływu na istniejące przydzielone porty SNAT i ich odpowiednie przepływy.

Alokacje portów SNAT są specyficzne dla protokołu transportu IP (TCP i UDP są obsługiwane oddzielnie) i są zwalniane pod następującymi warunkami:

### <a name="tcp-snat-port-release"></a>Wydanie portu TCP SNAT

- Jeśli którykolwiek serwer/klient wyśle FINACK, port SNAT zostanie zwolniony po 240 sekundach.
- Jeśli zostanie widoczna rst, port SNAT zostanie zwolniony po 15 sekundach.
- Jeśli limit czasu bezczynnego został osiągnięty, port jest zwalniany.

### <a name="udp-snat-port-release"></a>Wydanie portu UDP SNAT

- Jeśli limit czasu bezczynnego został osiągnięty, port jest zwalniany.

## <a name="problem-solving"></a><a name="problemsolving"></a>Rozwiązywanie problemów 

Ta sekcja ma na celu ograniczenie wyczerpania SNAT i może wystąpić w przypadku połączeń wychodzących na platformie Azure.

### <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>Zarządzanie wyczerpaniem portu SNAT (PAT)
[Porty efemeryczne](#preallocatedports) używane dla [PAT](#pat) są zasobem wyczerpującym, jak opisano w [autonomicznej maszynie wirtualnej bez publicznego adresu IP](#defaultsnat) i [maszyny wirtualnej z równoważenia obciążenia bez publicznego adresu IP](#lb). Możesz monitorować użycie portów tymczasowych i porównać z bieżącą alokacją, aby określić ryzyko lub potwierdzić ekshumację SNAT za pomocą [tego](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) przewodnika.

Jeśli wiesz, że inicjujesz wiele wychodzących połączeń TCP lub UDP do tego samego docelowego adresu IP i portu, a użytkownik obserwuje nieudane połączenia wychodzące lub jest informowany przez obsługę, że wyczerpujesz porty SNAT (wstępnie przydzielone [porty efemeryczne](#preallocatedports) używane przez [PAT),](#pat)masz kilka ogólnych opcji ograniczania ryzyka. Przejrzyj te opcje i zdecyduj, co jest dostępne i najlepsze dla twojego scenariusza. Jest możliwe, że jeden lub więcej może pomóc w zarządzaniu tym scenariuszem.

Jeśli masz problemy ze zrozumieniem zachowania połączenia wychodzącego, możesz użyć statystyk stosu IP (netstat). Lub może być pomocne obserwować zachowania połączeń za pomocą przechwytywania pakietów. Można wykonać te przechwytywanie pakietów w trybie operacyjnym gościa wystąpienia lub użyć [funkcji Kontrola sieci do przechwytywania pakietów](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

#### <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Modyfikowanie aplikacji w celu ponownego użycia połączeń 
Można zmniejszyć zapotrzebowanie na porty efemeryczne, które są używane dla SNAT przez ponowne użycie połączeń w aplikacji. Jest to szczególnie ważne w przypadku protokołów takich jak HTTP/1.1, gdzie ponowne użycie połączenia jest ustawieniem domyślnym. I inne protokoły, które używają HTTP jako ich transportu (na przykład REST) mogą korzystać z kolei. 

Ponowne użycie jest zawsze lepsze niż pojedyncze, niepodzielne połączenia TCP dla każdego żądania. Ponowne użycie skutkuje bardziej wydajnymi, bardzo wydajnymi transakcjami TCP.

#### <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Modyfikowanie aplikacji w celu użycia buforowania połączeń
Można zastosować schemat łączenia połączeń w aplikacji, gdzie żądania są wewnętrznie dystrybuowane między stałym zestawem połączeń (każdy ponownie, jeśli to możliwe). Ten schemat ogranicza liczbę portów efemerycznych w użyciu i tworzy bardziej przewidywalne środowisko. Ten schemat można również zwiększyć przepływność żądań, umożliwiając wiele jednoczesnych operacji, gdy jedno połączenie blokuje w odpowiedzi operacji.  

Buforowanie połączeń może już istnieć w ramach, który jest używany do tworzenia aplikacji lub ustawienia konfiguracji aplikacji. Łączenie połączeń można łączyć z ponownym użyciem połączenia. Wiele żądań zużywa następnie stałą, przewidywalną liczbę portów do tego samego docelowego adresu IP i portu. Żądania również korzystać z efektywnego wykorzystania transakcji TCP zmniejszenie opóźnienia i wykorzystania zasobów. Transakcje UDP mogą również korzystać, ponieważ zarządzanie liczbą przepływów UDP może z kolei uniknąć warunków wydechu i zarządzać wykorzystaniem portu SNAT.

#### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Modyfikowanie aplikacji w celu użycia mniej agresywnej logiki ponawiania prób
Gdy [wstępnie przydzielone porty efemeryczne](#preallocatedports) używane dla [PAT](#pat) są wyczerpane lub występują awarie aplikacji, agresywne lub brutalne próby sił bez logiki rozpadu i wycofywania powodują wystąpienie lub utrzymują się wyczerpanie. Można zmniejszyć zapotrzebowanie na porty efemeryczne przy użyciu logiki ponawiania mniej agresywne. 

Porty efemeryczne mają 4-minutowy limit czasu bezczynności (nie można regulować). Jeśli próby są zbyt agresywne, wyczerpanie nie ma możliwości, aby wyjaśnić na własną rękę. W związku z tym biorąc pod uwagę jak —-i jak często — aplikacja ponawia transakcje jest krytyczną częścią projektu.

#### <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Przypisywanie publicznego adresu IP do każdej maszyny Wirtualnej
Przypisywanie publicznego adresu IP zmienia scenariusz na [publiczny adres IP do maszyny Wirtualnej](#ilpip). Wszystkie porty efemeryczne publicznego adresu IP, które są używane dla każdej maszyny Wirtualnej są dostępne dla maszyny Wirtualnej. (W przeciwieństwie do scenariuszy, w których porty efemeryczne publicznego adresu IP są współużytkowane ze wszystkimi maszynami wirtualnymi skojarzonymi z odpowiednią pulą wewnętrznej bazy danych). Istnieją kompromisy do rozważenia, takie jak dodatkowe koszty publicznych adresów IP i potencjalny wpływ umieszczania na białej liście dużej liczby pojedynczych adresów IP.

>[!NOTE] 
>Ta opcja nie jest dostępna dla ról procesów roboczych sieci Web.

#### <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Używanie wielu frontendów

Korzystając z publicznego standardowego modułu równoważenia obciążenia, można przypisać [wiele adresów IP frontu dla połączeń wychodzących](#multife) i [pomnożyć liczbę dostępnych portów SNAT](#preallocatedports).  Utwórz konfigurację ip frontendu, regułę i pulę wewnętrznej bazy danych, aby wyzwolić programowanie SNAT do publicznego adresu IP frontendu.  Reguła nie musi działać i sondy kondycji nie musi się powieść.  Jeśli używasz wielu frontendów dla ruchu przychodzącego, jak również (a nie tylko dla wychodzących), należy użyć niestandardowych sond kondycji dobrze, aby zapewnić niezawodność.

>[!NOTE]
>W większości przypadków wyczerpanie portów SNAT jest oznaką złej konstrukcji.  Upewnij się, że rozumiesz, dlaczego wyczerpujesz porty przed użyciem większej liczby frontendów do dodania portów SNAT.  Być może maskujesz problem, który może później doprowadzić do awarii.

#### <a name="scale-out"></a><a name="scaleout"></a>Skalowanie w poziomie

[Wstępnie przydzielone porty](#preallocatedports) są przypisywane na podstawie rozmiaru puli wewnętrznej bazy danych i pogrupowane w warstwy, aby zminimalizować zakłócenia, gdy niektóre porty muszą zostać ponownie przydzielone, aby uwzględnić następną większą warstwę rozmiaru puli wewnętrznej bazy danych.  Może być opcja, aby zwiększyć intensywność wykorzystania portu SNAT dla danego frontonu przez skalowanie puli wewnętrznej bazy danych do maksymalnego rozmiaru dla danej warstwy.  Wymaga to dla aplikacji do skalowania w poziomie wydajnie.

Na przykład dwie maszyny wirtualne w puli wewnętrznej bazy danych będą miały 1024 porty SNAT dostępne dla konfiguracji IP, co pozwala na łącznie 2048 portów SNAT dla wdrożenia.  Jeśli wdrożenie miałoby zostać zwiększone do 50 maszyn wirtualnych, mimo że liczba wstępnie przydzielonych portów pozostaje stała na maszynie wirtualnej, w ramach wdrożenia może być użytych łącznie 51 200 (50 x 1024) portów SNAT.  Jeśli chcesz skalować w poziomie wdrożenia, sprawdź liczbę [wstępnie przydzielonych portów](#preallocatedports) na warstwę, aby upewnić się, że kształtujesz skalowanie w poziomie do maksimum dla odpowiedniej warstwy.  W poprzednim przykładzie, jeśli wybrano skalowanie w poziomie do 51 zamiast 50 wystąpień, należy przejść do następnej warstwy i skończyć z mniejszą liczbą portów SNAT na maszynę wirtualną, a także w sumie.

Jeśli skalowanie w poziomie do następnej warstwy rozmiaru puli większych wewnętrznej bazy danych, istnieje możliwość dla niektórych połączeń wychodzących do limit czasu, jeśli przydzielone porty muszą być ponownie przydzielone.  Jeśli używasz tylko niektóre porty SNAT, skalowanie w poziomie w następnej większej puli wewnętrznej bazy danych jest nieistotne.  Połowa istniejących portów zostanie ponownie przydzielona przy każdym przejściu do następnej warstwy puli wewnętrznej bazy danych.  Jeśli nie chcesz, aby miało to miejsce, należy kształtować wdrożenia do rozmiaru warstwy.  Lub upewnij się, że aplikacja może wykryć i ponowić próbę w razie potrzeby.  Keepalives TCP może pomóc w wykrywaniu, kiedy porty SNAT nie działają z powodu ponownego przydzielenia.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Użyj keepalives, aby zresetować limit czasu bezczynny ruchu wychodzącego

Połączenia wychodzące mają 4-minutowy limit czasu bezczynnego. Ten limit czasu można regulować za pomocą [reguł ruchu wychodzącego.](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout) Można również użyć transportu (na przykład TCP keepalives) lub keepalives warstwy aplikacji, aby odświeżyć przepływ bezczynny i zresetować ten limit czasu bezczynność, jeśli to konieczne.  

Podczas korzystania z TCP keepalives, wystarczy, aby włączyć je po jednej stronie połączenia. Na przykład wystarczy włączyć je po stronie serwera tylko zresetować czasomierz bezczynności przepływu i nie jest konieczne dla obu stron do zainicjowania TCP keepalives.  Podobne pojęcia istnieją dla warstwy aplikacji, w tym konfiguracji klient-serwer bazy danych.  Sprawdź po stronie serwera, jakie opcje istnieją dla aplikacji określonych keepalives.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Odnajdowanie publicznego adresu IP używanego przez maszynę wirtualną
Istnieje wiele sposobów określania publicznego źródłowego adresu IP połączenia wychodzącego. OpenDNS zapewnia usługę, która może wyświetlać publiczny adres IP maszyny Wirtualnej. 

Za pomocą polecenia nslookup można wysłać zapytanie DNS dotyczące nazwy myip.opendns.com do programu rozpoznawania nazw OpenDNS. Usługa zwraca źródłowy adres IP, który został użyty do wysłania kwerendy. Po uruchomieniu następującej kwerendy z maszyny Wirtualnej, odpowiedź jest publiczny adres IP używany dla tej maszyny Wirtualnej:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Zapobieganie łączności wychodzącej
Czasami jest niepożądane dla maszyny Wirtualnej, aby mieć możliwość tworzenia przepływu wychodzącego. Lub może istnieć wymóg, aby zarządzać, które miejsca docelowe mogą być osiągane za pomocą przepływów wychodzących lub które miejsca docelowe mogą rozpoczynać przepływy przychodzące. W takim przypadku można użyć [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md) do zarządzania miejscami docelowymi, do których może dotrzeć maszyna wirtualna. Można również użyć grup zabezpieczeń do zarządzania, które publiczne miejsce docelowe może inicjować przepływy przychodzące.

Po zastosowaniu sieciowej sieciowej sieciowej sieciowej do maszyny wirtualnej z równoważenia obciążenia należy zwrócić uwagę na [tagi usług](../virtual-network/security-overview.md#service-tags) i [domyślne reguły zabezpieczeń](../virtual-network/security-overview.md#default-security-rules). Należy upewnić się, że maszyna wirtualna może odbierać żądania sondy kondycji z modułu Azure Load Balancer. 

Jeśli grupa sieciowej sieciowej blokuje żądania sondy kondycji z tagu domyślnego AZURE_LOADBALANCER, sonda kondycji maszyny Wirtualnej kończy się niepowodzeniem, a maszyna wirtualna jest oznaczona w dół. Moduł równoważenia obciążenia przestaje wysyłać nowe przepływy do tej maszyny Wirtualnej.

## <a name="limitations"></a>Ograniczenia
- Role procesu roboczego sieci Web bez sieci wirtualnej i innych usług platformy firmy Microsoft mogą być dostępne, gdy tylko wewnętrzny standardowy moduł równoważenia obciążenia jest używany ze względu na efekt uboczny działania usług sieci pre-VNet i innych usług platformy. Nie należy polegać na tym efektie ubocznym, ponieważ sama usługa lub platforma bazowa może ulec zmianie bez powiadomienia. Należy zawsze zakładać, że należy utworzyć łączność wychodzącą jawnie w razie potrzeby podczas korzystania z wewnętrznego standardowego modułu równoważenia obciążenia tylko. Domyślny scenariusz [SNAT](#defaultsnat) 3 opisany w tym artykule nie jest dostępny.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Dowiedz się więcej o [regułach ruchu wychodzącego](load-balancer-outbound-rules-overview.md) dla standardowego publicznego modułu równoważenia obciążenia.
- Dowiedz się więcej o [modułze równoważenia obciążenia](load-balancer-overview.md).
- Dowiedz się więcej o [sieciowych grupach zabezpieczeń](../virtual-network/security-overview.md).
- Dowiedz się więcej o niektórych innych [funkcjach sieci kluczy](../networking/networking-overview.md) na platformie Azure.
