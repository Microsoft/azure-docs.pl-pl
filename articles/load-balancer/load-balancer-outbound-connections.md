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
ms.openlocfilehash: 0d16823f920695f84db74122c9a2ac07de0abdb2
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907078"
---
# <a name="outbound-connections-in-azure"></a>Połączenia wychodzące na platformie Azure

Azure Load Balancer zapewnia łączność wychodzącą dla wdrożeń klientów przy użyciu kilku różnych mechanizmów. W tym artykule opisano scenariusze, w których są one stosowane, jak działają i jak nimi zarządzać. Jeśli występują problemy z łącznością wychodzącą za pośrednictwem Azure Load Balancer, zobacz [Przewodnik rozwiązywania problemów dla połączeń wychodzących](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE] 
>W tym artykule omówiono tylko wdrożenia Menedżer zasobów. Przejrzyj [połączenia wychodzące (klasyczne)](load-balancer-outbound-connections-classic.md) dla wszystkich klasycznych scenariuszy wdrażania na platformie Azure.

Wdrożenie na platformie Azure może komunikować się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresów IP. Gdy wystąpienie inicjuje przepływ wychodzący do miejsca docelowego w publicznej przestrzeni adresów IP, platforma Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu tego mapowania ruch powrotny dla tego wychodzącego przepływu może również dotrzeć do prywatnego adresu IP, z którego pochodzi przepływ.

Na platformie Azure do wykonania tej funkcji jest stosowany translator adresów sieciowych (Resources). Gdy wiele prywatnych adresów IP jest rozliczanych za pojedynczym publicznym adresem IP, platforma Azure używa [translatora adresów portu (Binding)](#pat) do zamaskowanego prywatnych adresów IP. Porty tymczasowe są używane na potrzeby usługi i są [wstępnie przydzieloną](#preallocatedports) na podstawie rozmiaru puli.

Istnieje wiele [scenariuszy wychodzących](#scenarios). Te scenariusze można połączyć zgodnie z wymaganiami. Uważnie przejrzyj je, aby zrozumieć możliwości, ograniczenia i wzorce, które mają zastosowanie do modelu wdrożenia i scenariusza aplikacji. Zapoznaj się ze wskazówkami dotyczącymi [zarządzania tymi scenariuszami](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

>[!IMPORTANT] 
>Usługa Load Balancer w warstwie Standardowa i Standard publiczny adres IP wprowadzają nowe możliwości i inne zachowania łączności wychodzącej.  Nie są one takie same jak podstawowe jednostki SKU.  Jeśli chcesz mieć łączność wychodzącą podczas pracy z standardowymi jednostkami SKU, musisz jawnie zdefiniować ją przy użyciu standardowych publicznych adresów IP lub standardowych Load Balancer publicznych.  Obejmuje to tworzenie łączności wychodzącej przy użyciu wewnętrznego usługa Load Balancer w warstwie Standardowa.  Zalecamy, aby zawsze używać reguł ruchu wychodzącego dla standardowej Load Balancer publicznej.  [Scenariusz 3](#defaultsnat) nie jest dostępny w przypadku standardowej jednostki SKU.  Oznacza to, że w przypadku użycia wewnętrznego usługa Load Balancer w warstwie Standardowa należy wykonać kroki w celu utworzenia łączności wychodzącej dla maszyn wirtualnych w puli zaplecza, jeśli jest wymagana łączność wychodząca.  W kontekście łączności wychodzącej, pojedynczej autonomicznej maszyny wirtualnej, wszystkie maszyny wirtualne w zestawie dostępności, wszystkie wystąpienia w VMSS działają jako Grupa. Oznacza to, że jeśli jedna maszyna wirtualna w zestawie dostępności jest skojarzona ze standardową jednostką SKU, wszystkie wystąpienia maszyn wirtualnych w tym zestawie dostępności są teraz zgodne z tymi samymi regułami, tak jakby były one skojarzone ze standardową jednostką SKU, nawet jeśli pojedyncze wystąpienie nie jest bezpośrednio skojarzone z nim. To zachowanie jest również zaobserwowane w przypadku autonomicznej maszyny wirtualnej z wieloma kartami interfejsu sieciowego podłączonymi do modułu równoważenia obciążenia. Jeśli jedna karta sieciowa zostanie dodana jako autonomiczna, będzie miała takie samo zachowanie. Uważnie przejrzyj ten cały dokument, aby poznać ogólne koncepcje, przejrzyj [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md) pod kątem różnic między jednostkami SKU i przejrzyj [reguły ruchu wychodzącego](load-balancer-outbound-rules-overview.md).  Użycie reguł ruchu wychodzącego umożliwia precyzyjne sterowanie wszystkimi aspektami łączności wychodzącej.

## <a name="scenario-overview"></a><a name="scenarios"></a>Omówienie scenariusza

Azure Load Balancer i powiązane zasoby są jawnie definiowane podczas korzystania z [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Obecnie platforma Azure oferuje trzy różne metody, aby uzyskać łączność wychodzącą dla zasobów Azure Resource Manager. 

| Jednostki SKU | Scenariusz | Metoda | Protokoły IP | Opis |
| --- | --- | --- | --- | --- |
| Standardowa, podstawowa | [1. maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia (z lub bez Load Balancer)](#ilpip) | Reportcy adresów sieciowych, nie użyto zamaskowanego portu | TCP, UDP, ICMP, ESP | Platforma Azure używa publicznego adresu IP przypisanego do konfiguracji protokołu IP karty sieciowej tego wystąpienia. Dla tego wystąpienia są dostępne wszystkie porty tymczasowe. W przypadku korzystania z usługa Load Balancer w warstwie Standardowa [reguły wychodzące](load-balancer-outbound-rules-overview.md) nie są obsługiwane, jeśli publiczny adres IP jest przypisany do maszyny wirtualnej. |
| Standardowa, podstawowa | [2. publiczne Load Balancer skojarzone z maszyną wirtualną (brak publicznego adresu IP w wystąpieniu)](#lb) | Reportcy adresów sieciowych z użyciem zamaskowanego portu przy użyciu Load Balancer frontonów | TCP, UDP |Platforma Azure udostępnia publiczny adres IP publicznych frontonów Load Balancer z wieloma prywatnymi adresami IP. Platforma Azure używa tymczasowych portów frontonów do usługi. W przypadku korzystania z usługa Load Balancer w warstwie Standardowa należy zastosować [reguły ruchu](load-balancer-outbound-rules-overview.md) wychodzącego, aby jawnie zdefiniować łączność wychodzącą. |
| Brak lub podstawowa | [3. autonomiczna maszyna wirtualna (bez Load Balancer, brak publicznego adresu IP)](#defaultsnat) | Reportcy adresów sieciowych z zamaskowanem portów | TCP, UDP | Platforma Azure automatycznie wyznacza publiczny adres IP dla translatora adresów sieciowych, udostępnia ten publiczny adres IP z wieloma prywatnymi adresami IP zestawu dostępności i korzysta z portów tymczasowych tego publicznego adresu IP. Ten scenariusz jest alternatywą dla powyższych scenariuszy. Nie zalecamy jej, jeśli potrzebujesz widoczności i kontroli. |

Jeśli nie chcesz, aby maszyna wirtualna mogła komunikować się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresów IP, możesz użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) w celu zablokowania dostępu zgodnie z potrzebami. Sekcja [zapobiegająca łączności wychodzącej](#preventoutbound) omawia sieciowych grup zabezpieczeń bardziej szczegółowo. Wskazówki dotyczące projektowania i wdrażania sieci wirtualnej oraz zarządzania nią bez dostępu wychodzącego wykraczają poza zakres tego artykułu.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scenariusz 1. maszyna wirtualna z publicznym adresem IP

W tym scenariuszu maszyna wirtualna ma przypisany publiczny adres IP. Jeśli chodzi o połączenia wychodzące, nie ma znaczenia, czy maszyna wirtualna ma Równoważenie obciążenia, czy nie. Ten scenariusz ma pierwszeństwo przed innymi. Gdy jest używany publiczny adres IP, maszyna wirtualna używa publicznego adresu IP dla wszystkich przepływów wychodzących.  

Publiczny adres IP przypisany do maszyny wirtualnej jest relacją 1:1 (a nie 1: wiele) i zaimplementowaną jako bezstanowe urządzenie NAT 1:1.  Nie jest używane zamaskowane portów, a maszyna wirtualna ma dostęp do wszystkich tymczasowych portów do użycia.

Jeśli aplikacja inicjuje wiele przepływów wychodzących i jest używany wyczerpanie portów, należy rozważyć przypisanie [publicznego adresu IP do ograniczania ograniczeń dotyczących translatora adresów sieciowych](../load-balancer/troubleshoot-outbound-connection.md#assignilpip). Zapoznaj się z [zarządzaniem wyczerpaniem spalin](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) w całości.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scenariusz 2. maszyna wirtualna o zrównoważonym obciążeniu bez publicznego adresu IP

W tym scenariuszu maszyna wirtualna jest częścią publicznej puli zaplecza Load Balancer. Do maszyny wirtualnej nie jest przypisany publiczny adres IP. Zasób Load Balancer musi być skonfigurowany z regułą modułu równoważenia obciążenia, aby można było utworzyć połączenie między frontonem publicznego adresu IP a pulą zaplecza.

Jeśli ta konfiguracja reguły nie zostanie ukończona, zachowanie jest opisane w scenariuszu dla [autonomicznej maszyny wirtualnej bez publicznego adresu IP](#defaultsnat). Aby sonda kondycji zakończyła się powodzeniem, nie trzeba mieć działającego odbiornika w puli zaplecza.

Gdy maszyna wirtualna o zrównoważonym obciążeniu tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny adres IP przepływu wychodzącego na publiczny adres IP publicznego frontonu Load Balancer. Aby wykonać tę funkcję, platforma Azure używa tego programu. Platforma [Azure używa również](#pat) usługi, aby zamaskowane wiele prywatnych adresów IP za publicznym adresem IP. 

Tymczasowe porty frontonu publicznego adresu IP modułu równoważenia obciążenia są używane do rozróżniania poszczególnych przepływów pochodzących z maszyny wirtualnej. W przypadku tworzenia przepływów ruchu wychodzącego w ramach strumienia danych dynamicznie [przydzielono wstępnie przydzieloną porty](#preallocatedports) tymczasowe. W tym kontekście porty, które są używane do przystawcy adresów sieciowych, są nazywane portami.

Porty przystawek adresów sieciowych są wstępnie przydzielonymi zgodnie z opisem w sekcji [Omówienie i poznanie adresów sieciowych](#snat) . Są to skończone zasoby, które mogą być wyczerpane. Ważne jest, aby zrozumieć, jak są [używane](#pat). Aby dowiedzieć się, jak projektować w tym zużyciu i w razie potrzeby rozwiązać problem, przejrzyj [Zarządzanie wyczerpaniem](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)zasobów.

Gdy [wiele publicznych adresów IP jest skojarzonych z Load Balancer podstawowa](load-balancer-multivip-overview.md), każdy z tych publicznych adresów IP jest kandydatem do przepływów wychodzących, a jeden jest wybierany losowo.  

Aby monitorować kondycję połączeń wychodzących przy użyciu Load Balancer podstawowa, można użyć [dzienników Azure monitor dla](load-balancer-monitor-log.md) [dzienników zdarzeń Load Balancer i alertów](load-balancer-monitor-log.md#alert-event-log) do monitorowania komunikatów o wyczerpaniu portów.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scenariusz 3: autonomiczna maszyna wirtualna bez publicznego adresu IP

W tym scenariuszu maszyna wirtualna nie jest częścią publicznej puli Load Balancer (i nie jest częścią wewnętrznej puli usługa Load Balancer w warstwie Standardowa) i nie ma przypisanego publicznego adresu IP. Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny adres IP przepływu wychodzącego na publiczny źródłowy adres IP. Publiczny adres IP używany dla tego przepływu wychodzącego nie jest konfigurowalny i nie jest uwzględniany w limicie limitu zasobów publicznego adresu IP subskrypcji. Ten publiczny adres IP nie należy do Ciebie i nie może być zarezerwowany. Po ponownym wdrożeniu maszyny wirtualnej lub zestawu dostępności lub zestawu skalowania maszyn wirtualnych ten publiczny adres IP zostanie wyznaczony i będzie wymagany nowy publiczny adres IP. Nie należy używać tego scenariusza dla listy dozwolonych adresów IP. Zamiast tego należy użyć jednego z dwóch innych scenariuszy, w których jawnie deklaruje scenariusz wychodzący i publiczny adres IP, który ma być używany na potrzeby łączności wychodzącej.

>[!IMPORTANT] 
>Ten scenariusz ma zastosowanie również wtedy, gdy __tylko__ wewnętrzny Load Balancer jest dołączony. Scenariusz 3 __nie jest dostępny__ , gdy do maszyny wirtualnej jest dołączony wewnętrzny usługa Load Balancer w warstwie Standardowa.  Należy jawnie utworzyć [scenariusz 1](#ilpip) lub [Scenariusz 2](#lb) oprócz używania wewnętrznego usługa Load Balancer w warstwie Standardowa.

Aby można było wykonać tę funkcję, Platforma[PAT](#pat)Azure używa podszywającego się na portach. Ten scenariusz jest podobny do [scenariusza 2](#lb), z tą różnicą, że nie ma kontroli nad używanym adresem IP. Jest to scenariusz rezerwowy dla sytuacji, gdy scenariusze 1 i 2 nie istnieją. Nie zalecamy tego scenariusza, jeśli chcesz kontrolować adres wychodzący. Jeśli połączenia wychodzące są krytyczną częścią aplikacji, należy wybrać inny scenariusz.

Porty przydziałów adresów sieciowych są wstępnie przydzielonymi, zgodnie z opisem w sekcji [Omówienie adresów i](#snat) elementów.  Liczba maszyn wirtualnych, które udostępniają zestaw dostępności, określa, która warstwa alokacji wstępnej ma zastosowanie.  Autonomiczna maszyna wirtualna bez zestawu dostępności jest skuteczną pulą 1 na potrzeby określania wstępnego przydziału (porty podrzędnego 1024). Porty przyłączone do sieci to skończone zasoby, które mogą być wyczerpane. Ważne jest, aby zrozumieć, jak są [używane](#pat). Aby dowiedzieć się, jak projektować w tym zużyciu i w razie potrzeby rozwiązać problem, przejrzyj [Zarządzanie wyczerpaniem](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)zasobów.

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Wiele połączonych scenariuszy

Możesz połączyć scenariusze opisane w poprzednich sekcjach, aby osiągnąć konkretny wynik. Jeśli istnieją wiele scenariuszy, obowiązuje kolejność pierwszeństwa: [scenariusz 1](#ilpip) ma pierwszeństwo przed [Scenariusz 2](#lb) i [3](#defaultsnat). [Scenariusz 2](#lb) przesłania [Scenariusz 3](#defaultsnat).

Przykładem jest wdrożenie Azure Resource Manager, w którym aplikacja opiera się silnie na połączeniach wychodzących do ograniczonej liczby miejsc docelowych, ale również odbiera przepływy przychodzące w ramach Load Balancer frontonu. W takim przypadku można połączyć scenariusze 1 i 2 w celu zwolnienia. Aby uzyskać dodatkowe wzorce, przejrzyj temat [Zarządzanie wyczerpaniem adresów źródłowego](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

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

W przypadku wyczerpania zasobów portów współdziałania przepływy wychodzące kończą się niepowodzeniem, dopóki istniejące przepływy nie zwolnią portów. Load Balancer przejmowanie portów protokołu równorzędnego, gdy przepływ zostanie zamknięty i korzysta z [4-minutowego limitu czasu bezczynności](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) w przypadku odzyskiwania portów ze STRUMIENIAMI adresów sieciowych z bezczynnych przepływów.

Porty protokołu przesyłania adresów sieciowych UDP zwykle są znacznie szybsze niż porty protokołu przesyłania adresów sieciowych TCP ze względu na różnice w używanym algorytmie. Należy zaprojektować i skalować test z uwzględnieniem tej różnicy.

Aby uzyskać wzorce ograniczające warunki, które zwykle prowadzą do wyczerpania portów, przejrzyj sekcję [Zarządzanie](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) reportem adresów sieciowych.

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Wstępne przydzielanie portów tymczasowych dla podszywającego się portu

Platforma Azure używa algorytmu do określenia liczby wstępnie przyalokowanych portów przydziałów adresów sieciowych, które są dostępne na podstawie rozmiaru puli zaplecza przy użyciu[PAT](#pat)podzestawu adresów sieciowych z zamaskowanem portem. Porty IP to tymczasowe porty dostępne dla określonego publicznego adresu IP. Dla każdego publicznego adresu IP skojarzonego z modułem równoważenia obciążenia dostępne są 64 000 portów jako porty dla każdego protokołu transportu IP.

Ta sama liczba portów przydziałów adresów sieciowych jest wstępnie przypisana odpowiednio do protokołów UDP i TCP i zużywana niezależnie na protokół transportowy IP.  Niemniej jednak w zależności od tego, czy przepływ ma wartość UDP czy TCP, jest różne użycie portów.

>[!IMPORTANT]
>Programowanie źródłowego adresu IP jednostki SKU jest określane na podstawie protokołu transportowego i pochodzące z reguły równoważenia obciążenia.  Jeśli istnieje tylko reguła równoważenia obciążenia TCP, jest on dostępny tylko dla protokołu TCP. Jeśli masz tylko regułę równoważenia obciążenia TCP i potrzebujesz wychodzącego ruchu źródłowego dla protokołu UDP, Utwórz regułę równoważenia obciążenia UDP z tego samego frontonu w tej samej puli zaplecza.  Spowoduje to wyzwolenie programu do tworzenia adresów API dla protokołu UDP.  Reguła robocza lub sonda kondycji nie jest wymagana.  Podstawowy kluczowy adres IP jednostki SKU zawsze programuje w przypadku protokołu transportowego IPv4, niezależnie od protokołu transportu określonego w regule równoważenia obciążenia.

Platforma Azure wstępnie przydziela porty do konfiguracji protokołu IP kart sieciowych każdej maszyny wirtualnej. Po dodaniu konfiguracji adresu IP do puli, porty przydziałów adresów IP są wstępnie przydzielony do tej konfiguracji protokołu adresów IPv4 na podstawie rozmiaru puli zaplecza. Gdy przepływy wychodzące są [tworzone, za](#pat) pomocą tego portu są dynamicznie zużywane (do wstępnie przydzielonych limitów) i zwalniają te porty, gdy następuje zamknięcie przepływu lub [przekroczenie limitu czasu bezczynności](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) .

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

Należy pamiętać, że liczba dostępnych portów nie jest tłumaczona bezpośrednio na liczbę przepływów. Można ponownie użyć pojedynczego portu źródłowego dla wielu unikatowych miejsc docelowych. Porty są używane tylko wtedy, gdy konieczne jest przeprowadzenie unikatowych przepływów. Aby zapoznać się z zaleceniami dotyczącymi projektowania i rozwiązywania problemów, zapoznaj się z sekcją [jak zarządzać tym zasobem exhaustible](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) i sekcją opisującą [.](#pat)

Zmiana rozmiaru puli zaplecza może mieć wpływ na niektóre z określonych przepływów. Jeśli rozmiar puli zaplecza zwiększy się i przejdzie do następnej warstwy, połowa wstępnie przydzielonego portu ze współdzielonym zestawem danych jest odzyskiwana podczas przejścia do następnej większej warstwy puli zaplecza. Przepływy skojarzone z odwiązanym portem wiązania adresów sieciowych przekroczą limit czasu i muszą zostać ponownie nawiązane. Jeśli zostanie podjęta próba nowego przepływu, przepływ zakończy się pomyślnie, o ile dostępne są wstępnie przydzieloną porty.

W przypadku zmniejszenia rozmiaru puli zaplecza i przejścia do niższej warstwy liczba dostępnych portów podzestawów adresów sieciowych wzrasta. W takim przypadku nie ma to wpływ na istniejące przyłączone porty i ich przepływy.

Alokacje portów przydziałów adresów IP są zależne od protokołu NNTP (TCP i UDP są obsługiwane oddzielnie) i są publikowane w następujących warunkach:

### <a name="tcp-snat-port-release"></a>Wydanie portu źródłowego protokołu TCP

- Jeśli serwer/klient wysyła FINACK, port podu zostanie wydaną po 240 sekundach.
- Jeśli jest widoczny parametr RST, port elementu poddanego zostanie wystawiony po 15 sekundach.
- Po osiągnięciu limitu czasu bezczynności port zostanie wystawiony.

### <a name="udp-snat-port-release"></a>Wydanie portu przychodzący protokołu UDP

- Po osiągnięciu limitu czasu bezczynności port zostanie wystawiony.

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

## <a name="azure-load-balancer-outbound-rules"></a><a name="outboundrules"></a>Azure Load Balancer reguły ruchu wychodzącego

Azure Load Balancer zapewnia łączność wychodzącą z sieci wirtualnej oprócz ruchu przychodzącego.  Reguły ruchu wychodzącego ułatwiają konfigurowanie wychodzącej translacji adresów sieciowych [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)publicznej.  Masz pełną kontrolę deklaratywną nad łącznością wychodzącą w celu skalowania i dostrajania tej możliwości do konkretnych potrzeb.

![Load Balancer reguły ruchu wychodzącego](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Load Balancer reguły ruchu wychodzącego umożliwiają: 
- Zdefiniuj wychodzące NAT od podstaw.
- skalowanie i dostrajanie zachowania istniejącego wychodzącego translatora adresów sieciowych. 

Reguły ruchu wychodzącego umożliwiają sterowanie:
- które maszyny wirtualne powinny być tłumaczone na które publiczne adresy IP. 
- sposób przydzielenia [portów wychodzącego ruchu źródłowego](load-balancer-outbound-connections.md#snat) .
- protokoły, dla których ma zostać przewidziane tłumaczenie wychodzące.
- czas trwania okresu bezczynności połączenia wychodzącego (4-120 minut).
- Czy należy wysyłać Resetowanie protokołu TCP po upływie limitu czasu bezczynności

Reguły ruchu wychodzącego rozszerzają [Scenariusz 2](load-balancer-outbound-connections.md#lb) opisany w artykule [połączenia wychodzące](load-balancer-outbound-connections.md) , a priorytet scenariusza pozostaje taki, jak jest.

### <a name="outbound-rule"></a>Reguła ruchu wychodzącego

Podobnie jak w przypadku wszystkich reguł Load Balancer, reguły wychodzące mają taką samą znaną składnię jak równoważenie obciążenia i reguły NAT dla ruchu przychodzącego:

**fronton**  +  **Parametry**  +  **Pula zaplecza**

Reguła ruchu wychodzącego konfiguruje wychodzące NAT dla _wszystkich maszyn wirtualnych identyfikowanych przez pulę zaplecza_ , które mają zostać przetłumaczone na _fronton_.  I _Parametry_ zapewniają dodatkową kontrolę nad algorytmem NAT dla ruchu wychodzącego.

Wersja interfejsu API "2018-07-01" zezwala na strukturę definicji reguły ruchu wychodzącego w następujący sposób:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>Obowiązująca Konfiguracja NAT dla ruchu wychodzącego jest złożona ze wszystkich reguł ruchu wychodzącego i reguł równoważenia obciążenia. Reguły ruchu wychodzącego są przyrostowe w regułach równoważenia obciążenia. Zapoznaj się z tematem [wyłączanie wychodzącego NAT dla reguły równoważenia obciążenia](#disablesnat) , aby zarządzać efektywnym wychodzącym TRANSLACJą NAT w przypadku zastosowania wielu reguł do maszyny wirtualnej. Podczas definiowania reguły ruchu wychodzącego, która używa tego samego publicznego adresu IP jako reguły równoważenia obciążenia, należy [wyłączyć wychodzące](#disablesnat) obiektu zasad sieciowych.

#### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Skalowanie ruchu wychodzącego NAT z wieloma adresami IP

Reguła ruchu wychodzącego może być używana z tylko jednym publicznym adresem IP, ale reguły ruchu wychodzącego ułatwiają skalowanie w ramach ruchu wychodzącego NAT. Można użyć wielu adresów IP do zaplanowania scenariuszy o dużej skali i można użyć reguł ruchu wychodzącego, aby wyeliminować wzorce podatności na ruch [wydechowy](troubleshoot-outbound-connection.md#snatexhaust) .  

Każdy dodatkowy adres IP dostarczony przez fronton zapewnia 64 000 portów tymczasowych Load Balancer do użycia jako portów protokołu integracyjnego. Mimo że reguły równoważenia obciążenia lub ruchu przychodzącego NAT mają jedną fronton, reguła wychodząca rozszerza koncepcję frontonu i zezwala na wiele frontonów dla każdej reguły.  W przypadku wielu frontonów dla każdej reguły liczba dostępnych portów adresów IP jest mnożona przy użyciu każdego publicznego adresu i można obsługiwać duże scenariusze.

Ponadto można użyć [publicznego prefiksu adresu IP](https://aka.ms/lbpublicipprefix) bezpośrednio z regułą wychodzącą.  Używanie publicznego prefiksu adresu IP zapewnia łatwiejsze skalowanie i uproszczone Wyświetlanie listy przepływów pochodzących ze wdrożenia platformy Azure. Konfigurację adresu IP frontonu można skonfigurować w ramach zasobu Load Balancer, aby odwoływać się bezpośrednio do prefiksu publicznego adresu IP.  Pozwala to Load Balancer wyłącznej kontroli nad publicznym prefiksem adresu IP, a reguła wychodząca automatycznie będzie używać wszystkich publicznych adresów IP zawartych w publicznym prefiksie adresu IP dla połączeń wychodzących.  Każdy adres IP należący do zakresu publicznego prefiksu adresu IP zapewnia 64 000 portów tymczasowych na adres IP, aby Load Balancer używać jako portów podzestawów adresów.   

W przypadku korzystania z tej opcji nie można utworzyć pojedynczych zasobów publicznego adresu IP, które są tworzone na podstawie tego publicznego prefiksu IP, ponieważ reguła ruchu wychodzącego musi mieć pełną kontrolę nad publicznym prefiksem adresu IP.  Jeśli potrzebujesz bardziej precyzyjnej kontroli, możesz utworzyć pojedynczy zasób publicznego adresu IP na podstawie prefiksu publicznego adresu IP i przypisać wiele publicznych adresów IP pojedynczo do frontonu reguły wychodzącej.

#### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Dostrajanie alokacji portów dla współdzielonego

Możesz użyć reguł ruchu wychodzącego, aby dostroić [automatyczne przydzielanie portów adresów sieciowych w oparciu o rozmiar puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) i przydzielić więcej lub mniej niż jest to automatyczna alokacja portu.

Użyj następującego parametru, aby przydzielić porty protokołu IPSec 10 000 na maszynę wirtualną (Konfiguracja IP karty sieciowej).
 

          "allocatedOutboundPorts": 10000

Każdy publiczny adres IP ze wszystkich frontonów reguły wychodzącej współużytkuje do 64 000 tymczasowych portów do użycia jako porty protokołu reportowego.  Load Balancer przydziela porty przystawek adresów sieciowych w wielokrotnościach 8. Jeśli podano wartość, która nie jest podzielna przez 8, operacja konfiguracji zostanie odrzucona.  Jeśli podjęto próbę przydzielenia większej liczby portów ze standardem adresów IP, które są dostępne w oparciu o liczbę publicznych adresów, operacja konfiguracji zostanie odrzucona.  Na przykład w przypadku przydzielenia 10 000 portów na maszynę wirtualną i 7 maszyn wirtualnych w puli zaplecza współużytkują jeden publiczny 64 000 > 10 000 adres IP  Aby włączyć ten scenariusz, można dodać więcej publicznych adresów IP do frontonu reguły ruchu wychodzącego.

Można przywrócić [automatyczną alokację portu na podstawie rozmiaru puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) , określając 0 dla liczby portów. W takim przypadku pierwsze wystąpienia maszyn wirtualnych 50 będą mieć 1024 portów, a 51-100 wystąpienia maszyn wirtualnych otrzymają 512 i tak dalej, zgodnie z tabelą.

#### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Kontrola limitu czasu bezczynności przepływu wychodzącego

Reguły ruchu wychodzącego zapewniają parametr konfiguracji służący do kontrolowania limitu czasu bezczynności przepływu wychodzącego i dopasowania go do potrzeb aplikacji.  Domyślnie wychodzące limity czasu bezczynności to 4 minuty.  Parametr przyjmuje wartość od 4 do 120 do określonej liczby minut dla limitu czasu bezczynności dla przepływów pasujących do tej konkretnej reguły.

Użyj następującego parametru, aby ustawić limit czasu bezczynności dla ruchu wychodzącego na 1 godzinę:

          "idleTimeoutInMinutes": 60

#### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a>Włącz Resetowanie limitu czasu bezczynności dla protokołu TCP

Domyślnym zachowaniem Load Balancer jest odrzucanie przepływu w trybie dyskretnym, gdy osiągnięto limit czasu bezczynności wychodzący.  Za pomocą parametru enableTCPReset można włączyć bardziej przewidywalne zachowanie aplikacji i kontrolować, czy należy wysyłać dwukierunkowe Resetowanie protokołu TCP (TCP RST) w czasie poza limit czasu bezczynności ruchu wychodzącego. 

Użyj następującego parametru, aby włączyć Resetowanie protokołu TCP dla reguły ruchu wychodzącego:

           "enableTcpReset": true

Przejrzyj informacje [o limicie czasu bezczynności na potrzeby resetowania protokołu TCP](https://aka.ms/lbtcpreset) , co obejmuje dostępność regionów.

#### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Obsługa protokołów transportu TCP i UDP przy użyciu jednej reguły

Prawdopodobnie chcesz użyć "All" dla protokołu transportowego reguły ruchu wychodzącego, ale możesz również zastosować regułę ruchu wychodzącego do określonego protokołu transportowego, a także w razie potrzeby.

Użyj następującego parametru, aby ustawić protokół na TCP i UDP:

          "protocol": "All"

#### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Wyłącz wychodzące NAT dla reguły równoważenia obciążenia

Jak wspomniano wcześniej, reguły równoważenia obciążenia zapewniają automatyczne Programowanie dla wychodzącego translatora adresów sieciowych. Jednak niektóre scenariusze mogą korzystać z funkcji automatycznego programowania wychodzącego NAT przez regułę równoważenia obciążenia, co pozwala na kontrolowanie lub Uściślanie zachowania.  Reguły ruchu wychodzącego mają scenariusze, które są ważne, aby zatrzymać automatyczne wychodzące programowanie NAT.

Tego parametru można użyć na dwa sposoby:
- Opcjonalne pomijanie przy użyciu przychodzącego adresu IP dla wychodzącego translatora adresów sieciowych.  Reguły ruchu wychodzącego są przyrostowe do reguł równoważenia obciążenia i z tym zestawem parametrów Reguła ruchu wychodzącego jest w formancie.
  
- Dostosuj wychodzące parametry NAT adresu IP używanego do ruchu przychodzącego i wychodzącego jednocześnie.  Automatyczne wychodzące programowanie NAT musi być wyłączone, aby zezwolić regule wychodzącej na przejęcie kontroli.  Na przykład w celu zmiany alokacji portu podrzędnego adresu reportowego używany również dla ruchu przychodzącego ten parametr musi być ustawiony na wartość true.  Jeśli podjęto próbę użycia reguły wychodzącej w celu ponownego zdefiniowania parametrów adresu IP używanego do obsługi ruchu przychodzącego i nie wydano wychodzącego programowania NAT dla reguły równoważenia obciążenia, operacja konfigurowania reguły wychodzącej zakończy się niepowodzeniem.

>[!IMPORTANT]
> Jeśli ustawisz ten parametr na wartość true, maszyna wirtualna nie będzie mogła mieć łączności wychodzącej (lub [scenariusza publicznego adresu IP na poziomie wystąpienia](load-balancer-outbound-connections.md#ilpip) ), aby zdefiniować łączność wychodzącą.  Niektóre operacje związane z maszyną wirtualną lub aplikacją mogą zależeć od dostępnego połączenia wychodzącego. Zadbaj o to, aby zrozumieć zależności danego scenariusza i wziąć pod uwagę wpływ wprowadzania tej zmiany.

Dla reguły równoważenia obciążenia można wyłączyć ruch wychodzący z tego parametru konfiguracji:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametr disableOutboundSNAT ma wartość domyślną false, co oznacza, że reguła równoważenia **obciążenia zapewnia automatyczne** wychodzące NAT jako obraz lustrzany konfiguracji reguły równoważenia obciążenia.  

Jeśli ustawisz wartość disableOutboundSnat na true w regule równoważenia obciążenia, reguła równoważenia obciążenia zwalnia kontrolę nad automatycznym wychodzącym programowaniem NAT.  Wychodzące przychodzącego ruchu sieciowego w wyniku reguły równoważenia obciążenia jest wyłączone.

#### <a name="reuse-existing-or-define-new-backend-pools"></a>Ponowne używanie istniejących lub Definiowanie nowych pul zaplecza

Reguły ruchu wychodzącego nie wprowadzają nowej koncepcji w celu zdefiniowania grupy maszyn wirtualnych, do których ma zostać zastosowana reguła.  Zamiast tego ponownie używają koncepcji puli zaplecza, która jest również używana w regułach równoważenia obciążenia. Można jej użyć do uproszczenia konfiguracji przez ponowne użycie istniejącej definicji puli zaplecza lub utworzenie jednej dla reguły wychodzącej.

### <a name="scenarios"></a>Scenariusze

#### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Oczyść połączenia wychodzące do określonego zestawu publicznych adresów IP

Możesz użyć reguły ruchu wychodzącego, aby przetworzyć połączenia wychodzące w taki sposób, aby były wyświetlane z określonego zestawu publicznych adresów IP w celu ułatwienia listy dozwolonych scenariuszy.  Ten źródłowy publiczny adres IP może być taki sam, jak używany przez regułę równoważenia obciążenia lub inny zestaw publicznych adresów IP, niż jest używany przez regułę równoważenia obciążenia.  

1. Tworzenie [publicznego prefiksu adresu IP](https://aka.ms/lbpublicipprefix) (lub publicznych adresów IP z prefiksu publicznego adresu IP)
2. Tworzenie publicznej usługi Load Balancer w warstwie Standardowa
3. Utwórz frontony odwołujące się do publicznego prefiksu IP (lub publicznych adresów IP), których chcesz użyć
4. Użyj ponownie puli zaplecza lub Utwórz pulę zaplecza i umieść maszyny wirtualne w puli zaplecza Load Balancer publicznej
5. Skonfiguruj regułę ruchu wychodzącego na Load Balancer publicznej w celu zaprogramowania wychodzącego translatora adresów sieciowych dla tych maszyn wirtualnych przy użyciu frontonów
   
Jeśli nie chcesz, aby reguła równoważenia obciążenia była używana dla ruchu wychodzącego, należy [wyłączyć wychodzące elementu reźródłowego](#disablesnat) dla reguły równoważenia obciążenia.

#### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Modyfikuj przydział portu dla współdzielonego

Korzystając z reguł ruchu wychodzącego, można dostroić [automatyczną alokację portu z użyciem puli zaplecza](load-balancer-outbound-connections.md#preallocatedports).

Na przykład jeśli masz dwie maszyny wirtualne, które współużytkują jeden publiczny adres IP dla wychodzącego translatora adresów sieciowych, możesz zwiększyć liczbę portów przyznanych z domyślnych portów 1024, jeśli występuje wyczerpanie. Każdy publiczny adres IP może współtworzyć maksymalnie 64 000 portów tymczasowych.  W przypadku skonfigurowania reguły wychodzącej z jednym frontonem publicznego adresu IP można rozpowszechnić łącznie 64 000 portów do maszyn wirtualnych w puli zaplecza.  W przypadku dwóch maszyn wirtualnych maksymalnie 32 000 portów przydziałów adresów sieciowych można przydzielyć za pomocą reguły ruchu wychodzącego (2x 32 000 = 64 000).

Przejrzyj [połączenia wychodzące](load-balancer-outbound-connections.md) i szczegółowe informacje dotyczące sposobu przydzielania i używania portów podłączania [adresów sieciowych](load-balancer-outbound-connections.md#snat) .

#### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Włącz tylko wychodzące

Aby zapewnić wychodzące NAT dla grupy maszyn wirtualnych, można użyć usługa Load Balancer w warstwie Standardowa publicznej. W tym scenariuszu można użyć reguły wychodzącej sama przez siebie bez potrzeby stosowania dodatkowych reguł.

##### <a name="outbound-nat-for-vms-only-no-inbound"></a>Wychodzące NAT tylko dla maszyn wirtualnych (bez ruchu przychodzącego)

Zdefiniuj usługa Load Balancer w warstwie Standardowa publiczny, umieść maszyny wirtualne w puli zaplecza i skonfiguruj regułę ruchu wychodzącego w celu zaprogramowania wychodzących NAT i oczyść połączenia wychodzące z określonego publicznego adresu IP. Możesz również użyć publicznego prefiksu IP Uprość białe, aby wyświetlić listę źródeł połączeń wychodzących.

1. Utwórz publiczną usługa Load Balancer w warstwie Standardowa.
2. Utwórz pulę zaplecza i umieść maszyny wirtualne w puli zaplecza Load Balancer publicznej.
3. Skonfiguruj regułę ruchu wychodzącego na Load Balancer publicznej w celu zaprogramowania wychodzącego translatora adresów sieciowych dla tych maszyn wirtualnych.

##### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Wychodzące translatory adresów sieciowych dla wewnętrznych scenariuszy usługa Load Balancer w warstwie Standardowa

W przypadku korzystania z usługa Load Balancer w warstwie Standardowa wewnętrznego, wychodzący NAT nie jest dostępny do momentu jawnego zadeklarowania łączności wychodzącej. Można zdefiniować łączność wychodzącą przy użyciu reguły ruchu wychodzącego, aby utworzyć łączność wychodzącą dla maszyn wirtualnych za wewnętrzną usługa Load Balancer w warstwie Standardowa, wykonując następujące kroki:

1. Utwórz publiczną usługa Load Balancer w warstwie Standardowa.
2. Utwórz pulę zaplecza i umieść maszyny wirtualne w puli zaplecza Load Balancer publicznej oprócz wewnętrznego Load Balancer.
3. Skonfiguruj regułę ruchu wychodzącego na Load Balancer publicznej w celu zaprogramowania wychodzącego translatora adresów sieciowych dla tych maszyn wirtualnych.

##### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Włącz protokoły UDP & protokołu TCP dla ruchu wychodzącego NAT z usługa Load Balancer w warstwie Standardowa publiczny

- W przypadku korzystania z usługa Load Balancer w warstwie Standardowa publicznego automatyczne wychodzące programowanie NAT jest zgodne z protokołem transportu reguły równoważenia obciążenia.  

   1. Wyłącz przychodzący magazyn danych w regule równoważenia obciążenia.
   2. Skonfiguruj regułę wychodzącą na tym samym Load Balancer.
   3. Ponownie Użyj puli zaplecza już używanej przez maszyny wirtualne.
   4. Określ "Protokół": "All" jako część reguły ruchu wychodzącego.

- Gdy są używane tylko reguły NAT dla ruchu przychodzącego, nie jest dostarczany wychodzący translator adresów sieciowych.

   1. Umieść maszyny wirtualne w puli zaplecza.
   2. Zdefiniuj co najmniej jedną konfigurację adresu IP frontonu z publicznymi adresami IP lub prefiksem publicznego adresu IP.
   3. Skonfiguruj regułę wychodzącą na tym samym Load Balancer.
   4. Określ "Protocol": "All" jako część reguły ruchu wychodzącego


## <a name="limitations"></a>Ograniczenia
- Maksymalna liczba użytecznych portów tymczasowych na adres IP frontonu to 64 000.
- Zakres konfigurowalnego limitu czasu bezczynności wynosi od 4 do 120 minut (240 do 7200 sekund).
- Load Balancer nie obsługuje protokołu ICMP dla wychodzącego translatora adresów sieciowych.
- Reguły ruchu wychodzącego mogą być stosowane tylko do podstawowej konfiguracji adresu IP karty sieciowej.  Obsługiwane są wiele kart sieciowych.
- Role procesów roboczych sieci Web bez sieci wirtualnej i innych usług platformy firmy Microsoft mogą być dostępne, gdy jest używany tylko wewnętrzny usługa Load Balancer w warstwie Standardowa ze względu na efekt uboczny od sposobu, w jaki działają usługi frontonu i inne usługi platformy. Nie należy polegać na tym efekcie ubocznym, ponieważ sama sama usługa lub bazowa platforma mogą ulec zmianie bez powiadomienia. Zawsze należy założyć, że konieczne jest utworzenie połączenia wychodzącego jawnie, jeśli jest to potrzebne, przy użyciu tylko wewnętrznego usługa Load Balancer w warstwie Standardowa. Scenariusz [domyślny](#defaultsnat) dla tego artykułu nie jest dostępny.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Dowiedz się więcej o [regułach ruchu wychodzącego](load-balancer-outbound-rules-overview.md) dla standardowej Load Balancer publicznej.
- Dowiedz się więcej o [Load Balancer](load-balancer-overview.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
- Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) na platformie Azure.
