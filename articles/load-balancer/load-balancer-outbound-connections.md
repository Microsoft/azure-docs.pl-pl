---
title: Połączenia wychodzące na platformie Azure
titleSuffix: Azure Load Balancer
description: W tym artykule opisano, jak platforma Azure umożliwia maszynom wirtualnym komunikowanie się z publicznymi usługami internetowymi.
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
ms.openlocfilehash: 37a458aea659cb6215cf29e6abcbc3341c7e0b7b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83643253"
---
# <a name="outbound-connections-in-azure"></a>Połączenia wychodzące na platformie Azure

Platforma Azure zapewnia łączność wychodzącą dla wdrożeń klientów przy użyciu kilku różnych mechanizmów. W tym artykule opisano scenariusze, w których są one stosowane, jak działają i jak nimi zarządzać.

>[!NOTE] 
>W tym artykule omówiono tylko wdrożenia Menedżer zasobów. Przejrzyj [połączenia wychodzące (klasyczne)](load-balancer-outbound-connections-classic.md) dla wszystkich klasycznych scenariuszy wdrażania na platformie Azure.

Wdrożenie na platformie Azure może komunikować się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresów IP. Gdy wystąpienie inicjuje przepływ wychodzący do miejsca docelowego w publicznej przestrzeni adresów IP, platforma Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu tego mapowania ruch powrotny dla tego wychodzącego przepływu może również dotrzeć do prywatnego adresu IP, z którego pochodzi przepływ.

Na platformie Azure do wykonania tej funkcji jest stosowany translator adresów sieciowych (Resources). Gdy wiele prywatnych adresów IP jest rozliczanych za pojedynczym publicznym adresem IP, platforma Azure używa [translatora adresów portu (Binding)](#pat) do zamaskowanego prywatnych adresów IP. Porty tymczasowe są używane na potrzeby usługi i są [wstępnie przydzieloną](#preallocatedports) na podstawie rozmiaru puli.

Istnieje wiele [scenariuszy wychodzących](#scenarios). Te scenariusze można połączyć zgodnie z wymaganiami. Uważnie przejrzyj je, aby zrozumieć możliwości, ograniczenia i wzorce, które mają zastosowanie do modelu wdrożenia i scenariusza aplikacji. Zapoznaj się ze wskazówkami dotyczącymi [zarządzania tymi scenariuszami](#snatexhaust).

>[!IMPORTANT] 
>Usługa Load Balancer w warstwie Standardowa i Standard publiczny adres IP wprowadzają nowe możliwości i inne zachowania łączności wychodzącej.  Nie są one takie same jak podstawowe jednostki SKU.  Jeśli chcesz mieć łączność wychodzącą podczas pracy z standardowymi jednostkami SKU, musisz jawnie zdefiniować ją przy użyciu standardowych publicznych adresów IP lub standardowych Load Balancer publicznych.  Obejmuje to tworzenie łączności wychodzącej przy użyciu wewnętrznego usługa Load Balancer w warstwie Standardowa.  Zalecamy, aby zawsze używać reguł ruchu wychodzącego dla standardowej Load Balancer publicznej.  [Scenariusz 3](#defaultsnat) nie jest dostępny w przypadku standardowej jednostki SKU.  Oznacza to, że w przypadku użycia wewnętrznego usługa Load Balancer w warstwie Standardowa należy wykonać kroki w celu utworzenia łączności wychodzącej dla maszyn wirtualnych w puli zaplecza, jeśli jest wymagana łączność wychodząca.  W kontekście łączności wychodzącej, pojedynczej autonomicznej maszyny wirtualnej, wszystkie maszyny wirtualne w zestawie dostępności, wszystkie wystąpienia w VMSS działają jako Grupa. Oznacza to, że jeśli jedna maszyna wirtualna w zestawie dostępności jest skojarzona ze standardową jednostką SKU, wszystkie wystąpienia maszyn wirtualnych w tym zestawie dostępności są teraz zgodne z tymi samymi regułami, tak jakby były one skojarzone ze standardową jednostką SKU, nawet jeśli pojedyncze wystąpienie nie jest bezpośrednio skojarzone z nim. To zachowanie jest również zaobserwowane w przypadku autonomicznej maszyny wirtualnej z wieloma kartami interfejsu sieciowego podłączonymi do modułu równoważenia obciążenia. Jeśli jedna karta sieciowa zostanie dodana jako autonomiczna, będzie miała takie samo zachowanie. Uważnie przejrzyj ten cały dokument, aby poznać ogólne koncepcje, przejrzyj [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md) pod kątem różnic między jednostkami SKU i przejrzyj [reguły ruchu wychodzącego](load-balancer-outbound-rules-overview.md).  Użycie reguł ruchu wychodzącego umożliwia precyzyjne sterowanie wszystkimi aspektami łączności wychodzącej.

## <a name="scenario-overview"></a><a name="scenarios"></a>Omówienie scenariusza

Azure Load Balancer i powiązane zasoby są jawnie definiowane podczas korzystania z [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Obecnie platforma Azure oferuje trzy różne metody, aby uzyskać łączność wychodzącą dla zasobów Azure Resource Manager. 

| Jednostki SKU | Scenariusz | Metoda | Protokoły IP | Opis |
| --- | --- | --- | --- | --- |
| Standardowa, podstawowa | [1. maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia (z lub bez Load Balancer)](#ilpip) | Reportcy adresów sieciowych, nie użyto zamaskowanego portu | TCP, UDP, ICMP, ESP | Platforma Azure używa publicznego adresu IP przypisanego do konfiguracji protokołu IP karty sieciowej tego wystąpienia. Dla tego wystąpienia są dostępne wszystkie porty tymczasowe. W przypadku korzystania z usługa Load Balancer w warstwie Standardowa [reguły wychodzące](load-balancer-outbound-rules-overview.md) nie są obsługiwane, jeśli publiczny adres IP jest przypisany do maszyny wirtualnej. |
| Standardowa, podstawowa | [2. publiczne Load Balancer skojarzone z maszyną wirtualną (brak publicznego adresu IP w wystąpieniu)](#lb) | Reportcy adresów sieciowych z użyciem zamaskowanego portu przy użyciu Load Balancer frontonów | TCP, UDP |Platforma Azure udostępnia publiczny adres IP publicznych frontonów Load Balancer z wieloma prywatnymi adresami IP. Platforma Azure używa tymczasowych portów frontonów do usługi. Aby jawnie zdefiniować łączność wychodzącą, należy użyć [reguł ruchu wychodzącego](load-balancer-outbound-rules-overview.md) . |
| Brak lub podstawowa | [3. autonomiczna maszyna wirtualna (bez Load Balancer, brak publicznego adresu IP)](#defaultsnat) | Reportcy adresów sieciowych z zamaskowanem portów | TCP, UDP | Platforma Azure automatycznie wyznacza publiczny adres IP dla translatora adresów sieciowych, udostępnia ten publiczny adres IP z wieloma prywatnymi adresami IP zestawu dostępności i korzysta z portów tymczasowych tego publicznego adresu IP. Ten scenariusz jest alternatywą dla powyższych scenariuszy. Nie zalecamy jej, jeśli potrzebujesz widoczności i kontroli. |

Jeśli nie chcesz, aby maszyna wirtualna mogła komunikować się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresów IP, możesz użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) w celu zablokowania dostępu zgodnie z potrzebami. Sekcja [zapobiegająca łączności wychodzącej](#preventoutbound) omawia sieciowych grup zabezpieczeń bardziej szczegółowo. Wskazówki dotyczące projektowania i wdrażania sieci wirtualnej oraz zarządzania nią bez dostępu wychodzącego wykraczają poza zakres tego artykułu.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scenariusz 1. maszyna wirtualna z publicznym adresem IP

W tym scenariuszu maszyna wirtualna ma przypisany publiczny adres IP. Jeśli chodzi o połączenia wychodzące, nie ma znaczenia, czy maszyna wirtualna ma Równoważenie obciążenia, czy nie. Ten scenariusz ma pierwszeństwo przed innymi. Gdy jest używany publiczny adres IP, maszyna wirtualna używa publicznego adresu IP dla wszystkich przepływów wychodzących.  

Publiczny adres IP przypisany do maszyny wirtualnej jest relacją 1:1 (a nie 1: wiele) i zaimplementowaną jako bezstanowe urządzenie NAT 1:1.  Nie jest używane zamaskowane portów, a maszyna wirtualna ma dostęp do wszystkich tymczasowych portów do użycia.

Jeśli aplikacja inicjuje wiele przepływów wychodzących i jest używany wyczerpanie portów, należy rozważyć przypisanie [publicznego adresu IP do ograniczania ograniczeń dotyczących translatora adresów sieciowych](#assignilpip). Zapoznaj się z [zarządzaniem wyczerpaniem spalin](#snatexhaust) w całości.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scenariusz 2. maszyna wirtualna o zrównoważonym obciążeniu bez publicznego adresu IP

W tym scenariuszu maszyna wirtualna jest częścią publicznej puli zaplecza Load Balancer. Do maszyny wirtualnej nie jest przypisany publiczny adres IP. Zasób Load Balancer musi być skonfigurowany z regułą modułu równoważenia obciążenia, aby można było utworzyć połączenie między frontonem publicznego adresu IP a pulą zaplecza.

Jeśli ta konfiguracja reguły nie zostanie ukończona, zachowanie jest opisane w scenariuszu dla [autonomicznej maszyny wirtualnej bez publicznego adresu IP](#defaultsnat). Aby sonda kondycji zakończyła się powodzeniem, nie trzeba mieć działającego odbiornika w puli zaplecza.

Gdy maszyna wirtualna o zrównoważonym obciążeniu tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny adres IP przepływu wychodzącego na publiczny adres IP publicznego frontonu Load Balancer. Aby wykonać tę funkcję, platforma Azure używa tego programu. Platforma [Azure używa również](#pat) usługi, aby zamaskowane wiele prywatnych adresów IP za publicznym adresem IP. 

Tymczasowe porty frontonu publicznego adresu IP modułu równoważenia obciążenia są używane do rozróżniania poszczególnych przepływów pochodzących z maszyny wirtualnej. W przypadku tworzenia przepływów ruchu wychodzącego w ramach strumienia danych dynamicznie [przydzielono wstępnie przydzieloną porty](#preallocatedports) tymczasowe. W tym kontekście porty, które są używane do przystawcy adresów sieciowych, są nazywane portami.

Porty przystawek adresów sieciowych są wstępnie przydzielonymi zgodnie z opisem w sekcji [Omówienie i poznanie adresów sieciowych](#snat) . Są to skończone zasoby, które mogą być wyczerpane. Ważne jest, aby zrozumieć, jak są [używane](#pat). Aby dowiedzieć się, jak projektować w tym zużyciu i w razie potrzeby rozwiązać problem, przejrzyj [Zarządzanie wyczerpaniem](#snatexhaust)zasobów.

Gdy [wiele publicznych adresów IP jest skojarzonych z Load Balancer podstawowa](load-balancer-multivip-overview.md), każdy z tych publicznych adresów IP jest kandydatem do przepływów wychodzących, a jeden jest wybierany losowo.  

Aby monitorować kondycję połączeń wychodzących przy użyciu Load Balancer podstawowa, można użyć [dzienników Azure monitor dla](load-balancer-monitor-log.md) [dzienników zdarzeń Load Balancer i alertów](load-balancer-monitor-log.md#alert-event-log) do monitorowania komunikatów o wyczerpaniu portów.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scenariusz 3: autonomiczna maszyna wirtualna bez publicznego adresu IP

W tym scenariuszu maszyna wirtualna nie jest częścią publicznej puli Load Balancer (i nie jest częścią wewnętrznej puli usługa Load Balancer w warstwie Standardowa) i nie ma przypisanego publicznego adresu IP. Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny adres IP przepływu wychodzącego na publiczny źródłowy adres IP. Publiczny adres IP używany dla tego przepływu wychodzącego nie jest konfigurowalny i nie jest uwzględniany w limicie limitu zasobów publicznego adresu IP subskrypcji. Ten publiczny adres IP nie należy do Ciebie i nie może być zarezerwowany. Po ponownym wdrożeniu maszyny wirtualnej lub zestawu dostępności lub zestawu skalowania maszyn wirtualnych ten publiczny adres IP zostanie wyznaczony i będzie wymagany nowy publiczny adres IP. Nie należy używać tego scenariusza dla listy dozwolonych adresów IP. Zamiast tego należy użyć jednego z dwóch innych scenariuszy, w których jawnie deklaruje scenariusz wychodzący i publiczny adres IP, który ma być używany na potrzeby łączności wychodzącej.

>[!IMPORTANT] 
>Ten scenariusz ma zastosowanie również wtedy, gdy __tylko__ wewnętrzny Load Balancer jest dołączony. Scenariusz 3 __nie jest dostępny__ , gdy do maszyny wirtualnej jest dołączony wewnętrzny usługa Load Balancer w warstwie Standardowa.  Należy jawnie utworzyć [scenariusz 1](#ilpip) lub [Scenariusz 2](#lb) oprócz używania wewnętrznego usługa Load Balancer w warstwie Standardowa.

Aby można było wykonać tę funkcję, Platforma[PAT](#pat)Azure używa podszywającego się na portach. Ten scenariusz jest podobny do [scenariusza 2](#lb), z tą różnicą, że nie ma kontroli nad używanym adresem IP. Jest to scenariusz rezerwowy dla sytuacji, gdy scenariusze 1 i 2 nie istnieją. Nie zalecamy tego scenariusza, jeśli chcesz kontrolować adres wychodzący. Jeśli połączenia wychodzące są krytyczną częścią aplikacji, należy wybrać inny scenariusz.

Porty przydziałów adresów sieciowych są wstępnie przydzielonymi, zgodnie z opisem w sekcji [Omówienie adresów i](#snat) elementów.  Liczba maszyn wirtualnych, które udostępniają zestaw dostępności, określa, która warstwa alokacji wstępnej ma zastosowanie.  Autonomiczna maszyna wirtualna bez zestawu dostępności jest skuteczną pulą 1 na potrzeby określania wstępnego przydziału (porty podrzędnego 1024). Porty przyłączone do sieci to skończone zasoby, które mogą być wyczerpane. Ważne jest, aby zrozumieć, jak są [używane](#pat). Aby dowiedzieć się, jak projektować w tym zużyciu i w razie potrzeby rozwiązać problem, przejrzyj [Zarządzanie wyczerpaniem](#snatexhaust)zasobów.

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Wiele połączonych scenariuszy

Możesz połączyć scenariusze opisane w poprzednich sekcjach, aby osiągnąć konkretny wynik. Jeśli istnieją wiele scenariuszy, obowiązuje kolejność pierwszeństwa: [scenariusz 1](#ilpip) ma pierwszeństwo przed [Scenariusz 2](#lb) i [3](#defaultsnat). [Scenariusz 2](#lb) przesłania [Scenariusz 3](#defaultsnat).

Przykładem jest wdrożenie Azure Resource Manager, w którym aplikacja opiera się silnie na połączeniach wychodzących do ograniczonej liczby miejsc docelowych, ale również odbiera przepływy przychodzące w ramach Load Balancer frontonu. W takim przypadku można połączyć scenariusze 1 i 2 w celu zwolnienia. Aby uzyskać dodatkowe wzorce, przejrzyj temat [Zarządzanie wyczerpaniem adresów źródłowego](#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Wiele frontonów dla przepływów wychodzących

#### <a name="standard-load-balancer"></a>usługa Load Balancer w warstwie Standardowa

Usługa Load Balancer w warstwie Standardowa używa wszystkich kandydatów na przepływy wychodzące w tym samym czasie, gdy istnieje [wiele publicznych frontonów IP](load-balancer-multivip-overview.md) . Każdy fronton mnoży liczbę dostępnych wstępnie przyznanych portów podłączania, jeśli włączono regułę równoważenia obciążenia dla połączeń wychodzących.

Możesz pominąć użycie adresu IP frontonu dla połączeń wychodzących z nową opcją reguły równoważenia obciążenia:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Zwykle `disableOutboundSnat` opcja domyślnie ma _wartość false_ i oznacza, że ta reguła powoduje, że dla skojarzonych maszyn wirtualnych w puli zaplecza reguły równoważenia obciążenia są używane wychodzące pliki zasad sieciowych. `disableOutboundSnat`Można zmienić na _wartość true_ , aby uniemożliwić Load Balancer przy użyciu skojarzonego adresu IP frontonu dla połączeń wychodzących dla maszyn wirtualnych w puli zaplecza tej reguły równoważenia obciążenia.  Można również wyznaczyć określony adres IP dla przepływów wychodzących, jak opisano w [wielu połączonych scenariuszach](#combinations) .

#### <a name="load-balancer-basic"></a>Load Balancer podstawowa

Load Balancer podstawowa wybór jednego frontonu do użycia dla przepływów wychodzących, gdy [wiele głównych frontonów IP](load-balancer-multivip-overview.md) jest kandydatów dla przepływów wychodzących. Tego wyboru nie można skonfigurować i należy rozważyć użycie algorytmu wyboru jako losowo. Można wyznaczyć konkretny adres IP dla przepływów wychodzących, zgodnie z opisem w [wielu połączonych scenariuszach](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Strefy dostępności

W przypadku korzystania [z usługa Load Balancer w warstwie Standardowa z strefy dostępności](load-balancer-standard-availability-zones.md), nadmiarowe strefy frontonów mogą zapewnić bezproblemowy ruch wychodzący w strefie i obsłużyć niepowodzenie strefy.  Gdy są używane strefy frontony, połączenia wychodzącego do nich są udostępniane ze strefą, do której należą.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Informacje o porozumieniu i niepomyślnym

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Przymaskowany port do podszywającego się

Gdy publiczny zasób Load Balancer jest skojarzony z wystąpieniami maszyn wirtualnych, które nie mają dedykowanych publicznych adresów IP, wszystkie źródła połączenia wychodzącego zostaną ponownie napisana. Źródło jest ponownie zapisywane z prywatnej przestrzeni adresowej IP sieci wirtualnej na publiczny adres IP frontonu modułu równoważenia obciążenia. W publicznej przestrzeni adresowej IP, 5-krotka przepływu (źródłowy adres IP, port źródłowy, protokół transportu IP, docelowy adres IP, port docelowy) musi być unikatowa. Za pomocą protokołów TCP lub UDP można używać adresów IP maskujących port.

Porty tymczasowe (porty IP) są używane do osiągnięcia tego po zapisaniu prywatnego źródłowego adresu IP, ponieważ wiele przepływów pochodzi z jednego publicznego adresu IP. Algorytm podrzędnego podszywającego się portu przydziela porty protokołu podrzędnego inaczej dla połączeń UDP i TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>Porty źródłowego protokołu TCP

Jeden port strumienia adresów sieciowych jest używany w przepływie na jeden docelowy adres IP, port. W przypadku wielu przepływów TCP na ten sam docelowy adres IP, port i protokół każdy przepływ TCP zużywa pojedynczy port. Gwarantuje to, że przepływy są unikatowe, gdy pochodzą z tego samego publicznego adresu IP i przejdź do tego samego docelowego adresu IP, portu i protokołu. 

Wiele przepływów, każdy do innego docelowego adresu IP, portu i protokołu, współużytkuje jeden port. Docelowy adres IP, port i protokół sprawiają, że przepływy są unikatowe bez konieczności stosowania dodatkowych portów źródłowych do rozróżnienia przepływów w publicznej przestrzeni adresów IP.

#### <a name="udp-snat-ports"></a><a name="udp"></a>Porty źródłowego protokołu UDP

Porty przyłączone do protokołu UDP są zarządzane przez inny algorytm niż porty protokołu podlegającego TCP.  Load Balancer używa algorytmu znanego jako "Protokół NAT z ograniczeniami portu" dla protokołu UDP.  Jeden port dla każdego przepływu, niezależnie od docelowego adresu IP, portu.

#### <a name="snat-port-reuse"></a>Ponowne użycie portu dla translatora adresów sieciowych

Gdy port zostanie wystawiony, port będzie dostępny do ponownego użycia w razie potrzeby.  Porty w ramach usługi reportowe można traktować jako sekwencję od najniższego do najwyższego dostępnego dla danego scenariusza, a pierwszy dostępny port jest używany dla nowych połączeń. 
 
#### <a name="exhaustion"></a>Wyczerpania

W przypadku wyczerpania zasobów portów współdziałania przepływy wychodzące kończą się niepowodzeniem, dopóki istniejące przepływy nie zwolnią portów. Load Balancer przejmowanie portów protokołu równorzędnego, gdy przepływ zostanie zamknięty i korzysta z [4-minutowego limitu czasu bezczynności](#idletimeout) w przypadku odzyskiwania portów ze STRUMIENIAMI adresów sieciowych z bezczynnych przepływów.

Porty protokołu przesyłania adresów sieciowych UDP zwykle są znacznie szybsze niż porty protokołu przesyłania adresów sieciowych TCP ze względu na różnice w używanym algorytmie. Należy zaprojektować i skalować test z uwzględnieniem tej różnicy.

Aby uzyskać wzorce ograniczające warunki, które zwykle prowadzą do wyczerpania portów, przejrzyj sekcję [Zarządzanie](#snatexhaust) reportem adresów sieciowych.

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Wstępne przydzielanie portów tymczasowych dla podszywającego się portu

Platforma Azure używa algorytmu do określenia liczby wstępnie przyalokowanych portów przydziałów adresów sieciowych, które są dostępne na podstawie rozmiaru puli zaplecza przy użyciu[PAT](#pat)podzestawu adresów sieciowych z zamaskowanem portem. Porty IP to tymczasowe porty dostępne dla określonego publicznego adresu IP. Dla każdego publicznego adresu IP skojarzonego z modułem równoważenia obciążenia dostępne są 64 000 portów jako porty dla każdego protokołu transportu IP.

Ta sama liczba portów przydziałów adresów sieciowych jest wstępnie przypisana odpowiednio do protokołów UDP i TCP i zużywana niezależnie na protokół transportowy IP.  Niemniej jednak w zależności od tego, czy przepływ ma wartość UDP czy TCP, jest różne użycie portów.

>[!IMPORTANT]
>Programowanie źródłowego adresu IP jednostki SKU jest określane na podstawie protokołu transportowego i pochodzące z reguły równoważenia obciążenia.  Jeśli istnieje tylko reguła równoważenia obciążenia TCP, jest on dostępny tylko dla protokołu TCP. Jeśli masz tylko regułę równoważenia obciążenia TCP i potrzebujesz wychodzącego ruchu źródłowego dla protokołu UDP, Utwórz regułę równoważenia obciążenia UDP z tego samego frontonu w tej samej puli zaplecza.  Spowoduje to wyzwolenie programu do tworzenia adresów API dla protokołu UDP.  Reguła robocza lub sonda kondycji nie jest wymagana.  Podstawowy kluczowy adres IP jednostki SKU zawsze programuje w przypadku protokołu transportowego IPv4, niezależnie od protokołu transportu określonego w regule równoważenia obciążenia.

Platforma Azure wstępnie przydziela porty do konfiguracji protokołu IP kart sieciowych każdej maszyny wirtualnej. Po dodaniu konfiguracji adresu IP do puli, porty przydziałów adresów IP są wstępnie przydzielony do tej konfiguracji protokołu adresów IPv4 na podstawie rozmiaru puli zaplecza. Gdy przepływy wychodzące są [tworzone, za](#pat) pomocą tego portu są dynamicznie zużywane (do wstępnie przydzielonych limitów) i zwalniają te porty, gdy następuje zamknięcie przepływu lub [przekroczenie limitu czasu bezczynności](#idletimeout) .

W poniższej tabeli przedstawiono alokacje wstępne portów adresów sieciowych dla warstw rozmiaru puli zaplecza:

| Rozmiar puli (wystąpienia maszyn wirtualnych) | Wstępnie przydzielony porty dla konfiguracji protokołu IP|
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

>[!NOTE]
> W przypadku korzystania z usługa Load Balancer w warstwie Standardowa z [wieloma frontonami](load-balancer-multivip-overview.md)każdy adres IP frontonu mnoży liczbę dostępnych portów w poprzedniej tabeli. Na przykład Pula zaplecza z 50 maszyn wirtualnych z 2 regułami równoważenia obciążenia, z których każdy ma oddzielny adres IP frontonu, będzie używać portów adresów sieciowych 2048 (2x 1024) dla każdej reguły. Zobacz szczegóły dotyczące [wielu frontonów](#multife).

Należy pamiętać, że liczba dostępnych portów nie jest tłumaczona bezpośrednio na liczbę przepływów. Można ponownie użyć pojedynczego portu źródłowego dla wielu unikatowych miejsc docelowych. Porty są używane tylko wtedy, gdy konieczne jest przeprowadzenie unikatowych przepływów. Aby zapoznać się z zaleceniami dotyczącymi projektowania i rozwiązywania problemów, zapoznaj się z sekcją [jak zarządzać tym zasobem exhaustible](#snatexhaust) i sekcją opisującą [.](#pat)

Zmiana rozmiaru puli zaplecza może mieć wpływ na niektóre z określonych przepływów. Jeśli rozmiar puli zaplecza zwiększy się i przejdzie do następnej warstwy, połowa wstępnie przydzielonego portu ze współdzielonym zestawem danych jest odzyskiwana podczas przejścia do następnej większej warstwy puli zaplecza. Przepływy skojarzone z odwiązanym portem wiązania adresów sieciowych przekroczą limit czasu i muszą zostać ponownie nawiązane. Jeśli zostanie podjęta próba nowego przepływu, przepływ zakończy się pomyślnie, o ile dostępne są wstępnie przydzieloną porty.

W przypadku zmniejszenia rozmiaru puli zaplecza i przejścia do niższej warstwy liczba dostępnych portów podzestawów adresów sieciowych wzrasta. W takim przypadku nie ma to wpływ na istniejące przyłączone porty i ich przepływy.

Alokacje portów przydziałów adresów IP są zależne od protokołu NNTP (TCP i UDP są obsługiwane oddzielnie) i są publikowane w następujących warunkach:

### <a name="tcp-snat-port-release"></a>Wydanie portu źródłowego protokołu TCP

- Jeśli serwer/klient wysyła FINACK, port podu zostanie wydaną po 240 sekundach.
- Jeśli jest widoczny parametr RST, port elementu poddanego zostanie wystawiony po 15 sekundach.
- Po osiągnięciu limitu czasu bezczynności port zostanie wystawiony.

### <a name="udp-snat-port-release"></a>Wydanie portu przychodzący protokołu UDP

- Po osiągnięciu limitu czasu bezczynności port zostanie wystawiony.

## <a name="problem-solving"></a><a name="problemsolving"></a>Rozwiązywanie problemów 

Ta sekcja ma pomóc w ograniczeniu wyczerpania ruchu w zabezpieczeniach i może wystąpić w przypadku połączeń wychodzących na platformie Azure.

### <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>Zarządzanie wyczerpaniem portów (z)
[Porty](#preallocatedports) tymczasowe [używane do](#pat) nadania to zasób exhaustible, zgodnie z opisem w [autonomicznej maszynie wirtualnej bez publicznego adresu IP](#defaultsnat) i [maszyny wirtualnej ze zrównoważonym obciążeniem bez publicznego adresu IP](#lb). Możesz monitorować użycie portów tymczasowych i porównać z bieżącą alokacją, aby określić ryzyko dla lub potwierdzić wyczerpanie adresów w [tym](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) przewodniku.

Jeśli wiesz, że masz wiele wychodzących połączeń TCP lub UDP z tym samym docelowym adresem IP i portem, a następnie zauważysz, że połączenia wychodzące są zakończone niepowodzeniem lub są zalecane w przypadku wyczerpania [portów przydzielonego](#preallocatedports) [dostępu do danych](#pat)wyjściowych, masz kilka ogólnych opcji zaradczych. Zapoznaj się z tymi opcjami i zdecyduj, co jest dostępne i najlepsze dla Twojego scenariusza. Istnieje możliwość, że co najmniej jedna może pomóc w zarządzaniu tym scenariuszem.

Jeśli masz problemy z zrozumieniem zachowania połączenia wychodzącego, możesz użyć statystyk stosu IP (netstat). Może być również pomocne w obserwowanie zachowań połączenia za pomocą przechwytywania pakietów. Można wykonać te przechwycenia pakietu w systemie operacyjnym gościa wystąpienia lub użyć [Network Watcher do przechwytywania pakietów](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

#### <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Ręcznie Przydziel porty podrzędnego TRANSLATORa adresów sieciowych na maszynę wirtualną
Zgodnie z definicją w wstępnie [przydzielone porty](#preallocatedports)moduł równoważenia obciążenia automatycznie przydzieli porty na podstawie liczby maszyn wirtualnych w zapleczu. Domyślnie jest to wykonywane ostrożnie w celu zapewnienia skalowalności. Jeśli znasz maksymalną liczbę maszyn wirtualnych, które będą znajdować się w zapleczu, możesz ręcznie przydzielić porty przystawek w ramach każdej reguły ruchu wychodzącego. Na przykład, Jeśli wiesz, że masz maksymalnie 10 maszyn wirtualnych, możesz przydzielić 6 400 porty na maszynę wirtualną, a nie domyślną 1 024. 

#### <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Modyfikowanie aplikacji w celu ponownego użycia połączeń 
Można zmniejszyć zapotrzebowanie na porty tymczasowe, które są używane w przypadku usługi reportcy, przez ponowną obsługę połączeń w aplikacji. Jest to szczególnie prawdziwe w przypadku protokołów takich jak HTTP/1.1, w przypadku których użycie połączenia jest domyślne. Inne protokoły używające protokołu HTTP jako transportu (na przykład REST) mogą korzystać z tej usługi. 

Ponowne użycie jest zawsze lepsze niż pojedyncze, niepodzielne połączenia TCP dla każdego żądania. Wielokrotne użycie powoduje skuteczniejsze, bardzo wydajne transakcje TCP.

#### <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Modyfikowanie aplikacji tak, aby używała puli połączeń
Można użyć schematu puli połączeń w aplikacji, w którym żądania są dystrybuowane wewnętrznie w ramach ustalonego zestawu połączeń (z każdym z nich, gdzie to możliwe). Ten schemat ogranicza liczbę używanych portów tymczasowych i tworzy bardziej przewidywalne środowisko. Ten schemat może również zwiększyć przepływność żądań przez umożliwienie wielu równoczesnych operacji, gdy jedno połączenie blokuje odpowiedzi na odpowiedź operacji.  

Pule połączeń mogą już istnieć w ramach platformy, która jest używana do tworzenia aplikacji lub ustawień konfiguracji aplikacji. Pulę połączeń można połączyć z wielokrotnym użyciem połączenia. Wiele żądań korzysta z ustalonej, przewidywalnej liczby portów na ten sam docelowy adres IP i port. Żądania korzystają również z efektywnego wykorzystania transakcji TCP redukujących opóźnienia i wykorzystanie zasobów. Transakcje UDP mogą również być korzystne, ponieważ zarządzanie liczbą przepływów UDP może mieć na celu uniknięcie przekroczenia warunków i zarządzanie wykorzystaniem portów przystawek.

#### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Modyfikowanie aplikacji tak, aby była używana mniej agresywna logika ponawiania
Gdy [wstępnie alokowany porty](#preallocatedports) tymczasowe są używane na potrzeby odbierania [lub występują błędy](#pat) aplikacji, agresywne lub bezproblemowe wymuszanie ponawiania prób bez pozostałego działania lub logiki wycofywania. Możesz zmniejszyć zapotrzebowanie na tymczasowe porty przy użyciu mniej agresywnej logiki ponawiania. 

Porty tymczasowe mają 4-minutowy limit czasu bezczynności (nieregulowane). Jeśli ponowna próba jest zbyt agresywna, wyczerpanie nie ma możliwości samodzielnego wyczyszczenia. W związku z tym rozważać, jak i jak często — transakcje ponawiania prób aplikacji są istotną częścią projektu.

#### <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Przypisz publiczny adres IP do każdej maszyny wirtualnej
Przypisanie publicznego adresu IP zmienia Twój Scenariusz na [publiczny adres IP na maszynę wirtualną](#ilpip). Wszystkie tymczasowe porty publicznego adresu IP, które są używane dla każdej maszyny wirtualnej, są dostępne dla maszyny wirtualnej. (W przeciwieństwie do scenariuszy, w których porty tymczasowe publicznego adresu IP są udostępniane wszystkim maszynom wirtualnym skojarzonym z odpowiednią pulą zaplecza). Istnieją branże, które należy wziąć pod uwagę, takie jak dodatkowe koszty publicznych adresów IP i potencjalny wpływ listy dozwolonych dużej liczby pojedynczych adresów IP.

>[!NOTE] 
>Ta opcja nie jest dostępna dla ról procesów roboczych sieci Web.

#### <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Korzystanie z wielu frontonów

W przypadku korzystania z publicznej usługa Load Balancer w warstwie Standardowa należy przypisać [wiele adresów IP frontonu dla połączeń wychodzących](#multife) i [pomnożyć liczbę dostępnych portów](#preallocatedports).  Utwórz konfigurację adresu IP frontonu, regułę i pulę zaplecza, aby wyzwolić programowanie do publicznego adresu IP frontonu.  Reguła nie musi działać i sonda kondycji nie musi się powieść.  Jeśli używasz również wielu frontonów dla ruchu przychodzącego (a nie tylko dla ruchu wychodzącego), użyj niestandardowych sond kondycji, aby zapewnić niezawodność.

>[!NOTE]
>W większości przypadków wyczerpanie portów z podsiecią adresów sieciowych jest znakiem nieprawidłowego projektu.  Upewnij się, że rozumiesz, dlaczego są wyczerpani portów przed użyciem większej liczby frontonów do dodawania portów.  Może wystąpić problem z maską problemu, który może prowadzić do późniejszego błędu.

#### <a name="scale-out"></a><a name="scaleout"></a>Skalowanie w poziomie

Wstępnie [przydzielone porty](#preallocatedports) są przypisywane w oparciu o rozmiar puli zaplecza i pogrupowane w warstwy, aby zminimalizować zakłócenia, gdy niektóre porty muszą zostać ponownie przydzielone w celu uwzględnienia kolejnej większej warstwy rozmiaru puli zaplecza.  Może być dostępna opcja zwiększenia intensywności wykorzystania portów dla danego frontonu przez przeskalowanie puli zaplecza do maksymalnego rozmiaru danej warstwy.  Jest to wymagane w celu wydajnego skalowania aplikacji.

Na przykład dwie maszyny wirtualne w puli zaplecza będą mieć 1024 portów protokołu reportowego dla każdej konfiguracji IP, co pozwoli na całkowite porty przyłączone do 2048 dla wdrożenia.  Jeśli wdrożenie zostało zwiększone do 50 maszyn wirtualnych, mimo że liczba wstępnie przydzielonych portów pozostaje stała na maszynę wirtualną, wdrożenie może być używane przez łącznie 51 200 (50 x 1024) portów.  Jeśli chcesz skalować wdrożenie, sprawdź liczbę [wstępnie przydzieloną portów](#preallocatedports) na warstwę, aby upewnić się, że skalujesz skalę do wartości maksymalnej dla odpowiedniej warstwy.  W poprzednim przykładzie, jeśli wybrano opcję skalowania w poziomie do 51 zamiast wystąpień 50, należy postępować do następnej warstwy i zakończyć z mniejszą liczbą portów przystawek adresów sieciowych na maszynę wirtualną, a także łącznie.

W przypadku skalowania w poziomie do następnej większej warstwy rozmiaru puli zaplecza dla niektórych połączeń wychodzących istnieje możliwość przekroczenia limitu czasu w przypadku konieczności ponownej alokowania przyznanych portów.  Jeśli używasz tylko niektórych portów podzestawów adresów sieciowych, skalowanie w poziomie w następnym większym rozmiarze puli zaplecza jest nieaktualne.  Połowa istniejących portów zostanie przydzielona przy każdym przejściu do następnej warstwy puli zaplecza.  Jeśli nie chcesz, aby to zrobić, musisz pomieścić wdrożenie w rozmiarze warstwy.  Lub upewnij się, że aplikacja może wykryć i ponowić próbę w razie potrzeby.  Utrzymywanie aktywności protokołu TCP może pomóc w wykrywaniu, kiedy porty protokołu reportowego już nie działają z powodu ich ponownie przydzielenia.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Aby zresetować limit czasu bezczynności dla ruchu wychodzącego, użyj utrzymywania aktywności

Połączenia wychodzące mają 4-minutowy limit czasu bezczynności. Ten limit czasu jest dostosowywany za pośrednictwem [reguł ruchu wychodzącego](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout). W razie potrzeby można również użyć transportu (na przykład aktywności protokołu TCP) lub warstwy aplikacji, aby odświeżyć przepływ bezczynności i zresetować ten limit czasu bezczynności.  

W przypadku korzystania z funkcji utrzymywania aktywności protokołu TCP wystarczy włączyć je po jednej stronie połączenia. Na przykład wystarczy włączyć je po stronie serwera tylko w celu zresetowania czasomierza bezczynności przepływu i nie jest konieczne dla obu stron w celu zainicjowania obsługi protokołu TCP.  Podobne pojęcia istnieją dla warstwy aplikacji, w tym konfiguracje klienta bazy danych.  Sprawdź, czy po stronie serwera znajdują się opcje dotyczące nieaktywności specyficznych dla aplikacji.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Odnajdywanie publicznego adresu IP używanego przez maszynę wirtualną
Istnieje wiele sposobów określenia publicznego źródłowego adresu IP połączenia wychodzącego. OpenDNS zapewnia usługę, która może wyświetlać publiczny adres IP maszyny wirtualnej. 

Za pomocą polecenia nslookup można wysłać zapytanie DNS dla nazwy myip.opendns.com do OpenDNS rozpoznawania nazw. Usługa zwraca źródłowy adres IP, który został użyty do wysłania zapytania. Po uruchomieniu następującego zapytania z maszyny wirtualnej odpowiedź jest publicznym adresem IP używanym dla tej maszyny wirtualnej:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Zapobieganie łączności wychodzącej
Czasami jest niepożądane, aby maszyna wirtualna mogła utworzyć przepływ wychodzący. Może być również wymagane, aby można było zarządzać miejscami docelowymi z przepływami wychodzącymi lub które mogą zaczynać przepływy przychodzące. W takim przypadku można użyć [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md) do zarządzania miejscami docelowymi, do których maszyna wirtualna może uzyskać dostęp. Można również użyć sieciowych grup zabezpieczeń do zarządzania tym, które publiczne miejsce docelowe może inicjować przepływy przychodzące.

Po zastosowaniu sieciowej grupy zabezpieczeń do maszyny wirtualnej z równoważeniem obciążenia należy zwrócić uwagę na [Tagi usługi](../virtual-network/security-overview.md#service-tags) i [domyślne reguły zabezpieczeń](../virtual-network/security-overview.md#default-security-rules). Musisz się upewnić, że maszyna wirtualna może odbierać żądania sondowania kondycji z Azure Load Balancer. 

Jeśli sieciowej grupy zabezpieczeń blokuje żądania sondy kondycji z domyślnego tagu AZURE_LOADBALANCER, sonda kondycji maszyny wirtualnej kończy się niepowodzeniem, a maszyna wirtualna jest oznaczona jako wyłączona. Load Balancer przestaje wysyłać Nowe przepływy do tej maszyny wirtualnej.

## <a name="connections-to-azure-storage-in-the-same-region"></a>Połączenia z usługą Azure Storage w tym samym regionie

Połączenia wychodzące za pośrednictwem powyższych scenariuszy nie są wymagane do nawiązania połączenia z magazynem w tym samym regionie, w którym znajduje się maszyna wirtualna. Jeśli nie chcesz tego robić, użyj sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń), jak wyjaśniono powyżej. Łączność wychodząca jest wymagana w przypadku łączności z magazynem w innych regionach. Podczas nawiązywania połączenia z magazynem z maszyny wirtualnej w tym samym regionie źródłowy adres IP w dziennikach diagnostycznych magazynu będzie adresem wewnętrznym dostawcy, a nie publicznym adresem IP maszyny wirtualnej. Jeśli chcesz ograniczyć dostęp do konta magazynu do maszyn wirtualnych w co najmniej jednej podsieci Virtual Network w tym samym regionie, użyj [Virtual Network punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md) , a nie publicznego adresu IP podczas konfigurowania zapory konta magazynu. Po skonfigurowaniu punktów końcowych usługi zobaczysz Virtual Network prywatny adres IP w dziennikach diagnostycznych magazynu, a nie na wewnętrznym adresie dostawcy.

## <a name="limitations"></a>Ograniczenia
- Role procesów roboczych sieci Web bez sieci wirtualnej i innych usług platformy firmy Microsoft mogą być dostępne, gdy jest używany tylko wewnętrzny usługa Load Balancer w warstwie Standardowa ze względu na efekt uboczny od sposobu, w jaki działają usługi frontonu i inne usługi platformy. Nie należy polegać na tym efekcie ubocznym, ponieważ sama sama usługa lub bazowa platforma mogą ulec zmianie bez powiadomienia. Zawsze należy założyć, że konieczne jest utworzenie połączenia wychodzącego jawnie, jeśli jest to potrzebne, przy użyciu tylko wewnętrznego usługa Load Balancer w warstwie Standardowa. Scenariusz [domyślny](#defaultsnat) dla tego artykułu nie jest dostępny.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Dowiedz się więcej o [regułach ruchu wychodzącego](load-balancer-outbound-rules-overview.md) dla standardowej Load Balancer publicznej.
- Dowiedz się więcej o [Load Balancer](load-balancer-overview.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
- Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) na platformie Azure.
