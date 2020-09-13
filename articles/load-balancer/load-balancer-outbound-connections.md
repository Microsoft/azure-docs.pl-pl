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
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: 4368a025ecc158afa1ee78b8abd86bd6db42ba75
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438669"
---
# <a name="outbound-connections-in-azure"></a>Połączenia wychodzące na platformie Azure

Azure Load Balancer zapewnia łączność wychodzącą za pośrednictwem różnych mechanizmów. W tym artykule opisano scenariusze i sposoby zarządzania nimi. Jeśli występują problemy z łącznością wychodzącą za pośrednictwem Azure Load Balancer, zobacz [Przewodnik rozwiązywania problemów dla połączeń wychodzących](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE]
>W tym artykule opisano Menedżer zasobów wdrożeniach. Firma Microsoft zaleca Menedżer zasobów do obciążeń produkcyjnych.

## <a name="terminology"></a>Terminologia

| Okres | Odpowiednie protokoły | Szczegóły|
| ---------|---------| -------|
| Translator adresów sieciowych (Resources) | TCP, UDP | Wdrożenie na platformie Azure może komunikować się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresów IP. Gdy wystąpienie inicjuje przepływ wychodzący do miejsca docelowego w publicznej przestrzeni adresów IP, platforma Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu tego mapowania ruch powrotny dla tego wychodzącego przepływu może również dotrzeć do prywatnego adresu IP, z którego pochodzi przepływ. Na platformie Azure do wykonania tej funkcji jest stosowany **translator adresów sieciowych (Resources)** .|
| Przymaskowany port do podszywającego się| TCP, UDP |  Gdy wiele prywatnych adresów IP jest rozliczanych za pojedynczym publicznym adresem IP, platforma Azure korzysta z **translacji adresów portu (** w celu zamaskowanego/ukrycia prywatnych adresów IP). Porty tymczasowe są używane na potrzeby usługi i są [wstępnie przydzieloną](#preallocatedports) na podstawie rozmiaru puli. Gdy publiczny zasób Load Balancer jest skojarzony z wystąpieniami maszyn wirtualnych, które nie mają dedykowanych publicznych adresów IP, wszystkie źródła połączenia wychodzącego zostaną ponownie napisana. Źródło jest ponownie zapisywane z prywatnej przestrzeni adresowej IP sieci wirtualnej na publiczny adres IP frontonu modułu równoważenia obciążenia. W publicznej przestrzeni adresowej IP, 5-krotka przepływu (źródłowy adres IP, port źródłowy, protokół transportu IP, docelowy adres IP, port docelowy) musi być unikatowa. Za pomocą protokołów TCP lub UDP można używać adresów IP maskujących port. Porty tymczasowe (porty IP) są używane do osiągnięcia tego po zapisaniu prywatnego źródłowego adresu IP, ponieważ wiele przepływów pochodzi z jednego publicznego adresu IP. Algorytm podrzędnego podszywającego się portu przydziela porty protokołu podrzędnego inaczej dla połączeń UDP i TCP.|
| Porty źródłowego translatora adresów sieciowych| TCP | Porty IP to tymczasowe porty dostępne dla określonego publicznego adresu IP. Jeden port strumienia adresów sieciowych jest używany w przepływie na jeden docelowy adres IP, port. W przypadku wielu przepływów TCP na ten sam docelowy adres IP, port i protokół każdy przepływ TCP zużywa pojedynczy port. Gwarantuje to, że przepływy są unikatowe, gdy pochodzą z tego samego publicznego adresu IP i przejdź do tego samego docelowego adresu IP, portu i protokołu. Wiele przepływów, każdy do innego docelowego adresu IP, portu i protokołu, współużytkuje jeden port. Docelowy adres IP, port i protokół sprawiają, że przepływy są unikatowe bez konieczności stosowania dodatkowych portów źródłowych do rozróżnienia przepływów w publicznej przestrzeni adresów IP.|
|Porty źródłowego translatora adresów sieciowych | UDP | Porty przyłączone do protokołu UDP są zarządzane przez inny algorytm niż porty protokołu podlegającego TCP.  Load Balancer używa algorytmu znanego jako "Protokół NAT z ograniczeniami portu" dla protokołu UDP.  Jeden port dla każdego przepływu, niezależnie od docelowego adresu IP, portu.|
| Wyczerpania | - | W przypadku wyczerpania zasobów portów współdziałania przepływy wychodzące kończą się niepowodzeniem, dopóki istniejące przepływy nie zwolnią portów. Load Balancer przejmowanie portów protokołu równorzędnego, gdy przepływ zostanie zamknięty i korzysta z [4-minutowego limitu czasu bezczynności](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) w przypadku odzyskiwania portów ze STRUMIENIAMI adresów sieciowych z bezczynnych przepływów. Porty protokołu przesyłania adresów sieciowych UDP zwykle są znacznie szybsze niż porty protokołu przesyłania adresów sieciowych TCP ze względu na różnice w używanym algorytmie. Należy zaprojektować i skalować test z uwzględnieniem tej różnicy.|
| Zachowanie wersji portu przez przytranslatora adresów sieciowych | TCP | Jeśli serwer/klient wysyła FINACK, port podu zostanie wydaną po 240 sekundach. Jeśli jest widoczny parametr RST, port elementu poddanego zostanie wystawiony po 15 sekundach. Po osiągnięciu limitu czasu bezczynności port zostanie wystawiony.|
| Zachowanie wersji portu przez przytranslatora adresów sieciowych | UDP |Po osiągnięciu limitu czasu bezczynności port zostanie wystawiony.|
| Ponowne użycie portu dla translatora adresów sieciowych | TCP, UDP | Gdy port zostanie wystawiony, port będzie dostępny do ponownego użycia w razie potrzeby.  Porty w ramach usługi reportowe można traktować jako sekwencję od najniższego do najwyższego dostępnego dla danego scenariusza, a pierwszy dostępny port jest używany dla nowych połączeń.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algorytm alokacji portów

Platforma Azure używa algorytmu, aby określić liczbę wstępnie przyalokowanych portów podzestawów adresów sieciowych dostępnych na podstawie rozmiaru puli zaplecza podczas korzystania z usługi. Dla każdego publicznego adresu IP skojarzonego z modułem równoważenia obciążenia dostępne są 64 000 portów jako porty dla każdego protokołu transportu IP. Ta sama liczba portów przydziałów adresów sieciowych jest wstępnie przypisana odpowiednio do protokołów UDP i TCP i zużywana niezależnie na protokół transportowy IP.  Niemniej jednak w zależności od tego, czy przepływ ma wartość UDP czy TCP, jest różne użycie portów. Gdy przepływy wychodzące są tworzone, te porty są używane dynamicznie (do wstępnie przydzielonych limitów) i wydawane, gdy następuje zamknięcie przepływu lub [przekroczenie limitu czasu bezczynności](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) . Porty są używane tylko wtedy, gdy konieczne jest przeprowadzenie unikatowych przepływów.

#### <a name="dynamic-snat-ports-allocated"></a><a name="snatporttable"></a> Przydzielono porty dynamicznego translatora adresów sieciowych

W poniższej tabeli przedstawiono alokacje wstępne portów adresów sieciowych dla warstw rozmiaru puli zaplecza:

| Rozmiar puli (wystąpienia maszyn wirtualnych) | Wstępnie przydzielony porty dla konfiguracji protokołu IP |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Zmiana rozmiaru puli zaplecza może mieć wpływ na niektóre ustanowione przepływy:

- Jeśli rozmiar puli zaplecza zwiększy się i przejdzie do następnej warstwy, połowa wstępnie przydzielonego portu ze współdzielonym zestawem danych jest odzyskiwana podczas przejścia do następnej większej warstwy puli zaplecza. Przepływy skojarzone z odwiązanym portem wiązania adresów sieciowych przekroczą limit czasu i muszą zostać ponownie nawiązane. Jeśli zostanie podjęta próba nowego przepływu, przepływ zakończy się pomyślnie, o ile dostępne są wstępnie przydzieloną porty.
- W przypadku zmniejszenia rozmiaru puli zaplecza i przejścia do niższej warstwy liczba dostępnych portów podzestawów adresów sieciowych wzrasta. W takim przypadku nie ma to wpływ na istniejące przyłączone porty i ich przepływy.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Omówienie scenariusza połączeń wychodzących

| Scenariusz | Metoda | Protokoły IP | Opis |
|  --- | --- | --- | --- |
|  1. maszyna wirtualna z publicznym adresem IP (z Azure Load Balancer lub bez niej) | Reportcy adresów sieciowych, nie użyto zamaskowanego portu | TCP, UDP, ICMP, ESP | Platforma Azure używa publicznego adresu IP przypisanego do konfiguracji protokołu IP karty sieciowej wystąpienia dla wszystkich przepływów wychodzących. Dla tego wystąpienia są dostępne wszystkie porty tymczasowe. Nie ma znaczenia, czy maszyna wirtualna ma Równoważenie obciążenia, czy nie. Ten scenariusz ma pierwszeństwo przed innymi. Publiczny adres IP przypisany do maszyny wirtualnej jest relacją 1:1 (a nie 1: wiele) i zaimplementowaną jako bezstanowe urządzenie NAT 1:1. |
| 2. publiczne Load Balancer skojarzone z maszyną wirtualną (brak publicznego adresu IP na maszynie wirtualnej/wystąpieniu) | Reportcy adresów sieciowych z użyciem zamaskowanego portu przy użyciu Load Balancer frontonów | TCP, UDP | W tym scenariuszu zasób Load Balancer musi być skonfigurowany za pomocą reguły modułu równoważenia obciążenia, aby utworzyć łącze między publicznym serwerem frontonu IP a pulą zaplecza. Jeśli ta konfiguracja reguły nie zostanie ukończona, zachowanie jest zgodne z opisem w scenariuszu 3. Aby sonda kondycji zakończyła się powodzeniem, nie trzeba mieć działającego odbiornika w puli zaplecza. Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny adres IP przepływu wychodzącego na publiczny adres IP publicznego frontonu Load Balancerego za pośrednictwem protokołu resourceer. Tymczasowe porty dla publicznego adresu IP frontonu modułu równoważenia obciążenia są używane do rozróżniania poszczególnych przepływów pochodzących z maszyny wirtualnej. W przypadku tworzenia przepływów ruchu wychodzącego w ramach strumienia danych dynamicznie [przydzielono wstępnie przydzieloną porty](#preallocatedports) tymczasowe. W tym kontekście porty, które są używane do przystawcy adresów sieciowych, są nazywane portami. Porty przydziałów adresów sieciowych są wstępnie przydzielonymi zgodnie z opisem w [tabeli domyślnych przyznanych portów](#snatporttable). |
| 3. maszyna wirtualna (bez Load Balancer, brak publicznego adresu IP) lub maszyna wirtualna skojarzona z podstawowym Load Balancer wewnętrznym | Reportcy adresów sieciowych z zamaskowanem portów | TCP, UDP | Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny adres IP przepływu wychodzącego na publiczny źródłowy adres IP. Ten publiczny adres IP **nie jest konfigurowalny**, nie można go zarezerwować i nie jest uwzględniany w limicie limitu zasobów publicznego adresu IP subskrypcji. Po ponownym wdrożeniu maszyny wirtualnej lub zestawu dostępności lub zestawu skalowania maszyn wirtualnych ten publiczny adres IP zostanie wyznaczony i będzie wymagany nowy publiczny adres IP. Nie należy używać tego scenariusza dla listy dozwolonych adresów IP. Zamiast tego należy użyć scenariusza 1 lub 2, gdzie jawnie deklaruje zachowanie wychodzące. Porty przydziałów adresów sieciowych są wstępnie przydzielonymi zgodnie z opisem w [tabeli domyślnych przyznanych portów](#snatporttable).

## <a name="outbound-rules"></a><a name="outboundrules"></a>Reguły ruchu wychodzącego

 Reguły ruchu wychodzącego ułatwiają konfigurowanie wychodzącej translacji adresów sieciowych [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)publicznej.  Masz pełną kontrolę deklaratywną nad łącznością wychodzącą w celu skalowania i dostrajania tej możliwości do konkretnych potrzeb. W tej sekcji opisano scenariusz 2 (B) opisany powyżej.

![Load Balancer reguły ruchu wychodzącego](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Korzystając z reguł ruchu wychodzącego, można definiować wychodzące NAT od podstaw za pomocą Load Balancer. Możesz również skalować i dostrajać zachowanie istniejącego wychodzącego translatora adresów sieciowych.

Reguły ruchu wychodzącego umożliwiają sterowanie:

- które maszyny wirtualne powinny być tłumaczone na które publiczne adresy IP.
- sposób przydzielenia portów wychodzącego ruchu źródłowego.
- protokoły, dla których ma zostać przewidziane tłumaczenie wychodzące.
- czas trwania okresu bezczynności połączenia wychodzącego (4-120 minut).
- Czy należy wysyłać Resetowanie protokołu TCP po upływie limitu czasu bezczynności
- protokoły transportowe TCP i UDP z pojedynczą regułą

### <a name="outbound-rule-definition"></a>Definicja reguły ruchu wychodzącego

Podobnie jak w przypadku wszystkich reguł Load Balancer, reguły wychodzące mają taką samą znaną składnię jak równoważenie **frontend**obciążenia i reguły NAT dla ruchu przychodzącego:  +  **parameters**  +  **Pula zaplecza**parametrów frontonu. Reguła ruchu wychodzącego konfiguruje wychodzące NAT dla _wszystkich maszyn wirtualnych identyfikowanych przez pulę zaplecza_ , które mają zostać przetłumaczone na _fronton_.  _Parametry_ zapewniają dodatkową kontrolę nad algorytmem NAT dla ruchu wychodzącego.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Skalowanie ruchu wychodzącego NAT z wieloma adresami IP

Każdy dodatkowy adres IP dostarczony przez fronton zapewnia dodatkowe 64 000 Load Balancer portów tymczasowych, które mają być używane jako porty IP. Można użyć wielu adresów IP do zaplanowania scenariuszy o dużej skali i można użyć reguł ruchu wychodzącego, aby wyeliminować wzorce podatności na ruch [wydechowy](troubleshoot-outbound-connection.md#snatexhaust) .  

Można również użyć [publicznego prefiksu adresu IP](https://aka.ms/lbpublicipprefix) bezpośrednio z regułą wychodzącą.  Używanie publicznego prefiksu adresu IP zapewnia łatwiejsze skalowanie i uproszczone Wyświetlanie listy przepływów pochodzących ze wdrożenia platformy Azure. Konfigurację adresu IP frontonu można skonfigurować w ramach zasobu Load Balancer, aby odwoływać się bezpośrednio do prefiksu publicznego adresu IP.  Pozwala to Load Balancer wyłącznej kontroli nad publicznym prefiksem adresu IP, a reguła wychodząca automatycznie będzie używać wszystkich publicznych adresów IP zawartych w publicznym prefiksie adresu IP dla połączeń wychodzących.  Każdy adres IP w ramach publicznego prefiksu IP zapewnia dodatkowe 64 000 portów tymczasowych dla każdego adresu IP, aby Load Balancer używać jako portów protokołu reportowego.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Limit czasu bezczynności przepływu ruchu wychodzącego i Resetowanie protokołu TCP

Reguły ruchu wychodzącego zapewniają parametr konfiguracji służący do kontrolowania limitu czasu bezczynności przepływu wychodzącego i dopasowania go do potrzeb aplikacji. Domyślnie wychodzące limity czasu bezczynności to 4 minuty. Możesz dowiedzieć się, jak [skonfigurować limity czasu bezczynności](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). Domyślnym zachowaniem Load Balancer jest odrzucanie przepływu w trybie dyskretnym, gdy osiągnięto limit czasu bezczynności wychodzący.  Za pomocą `enableTCPReset` parametru można włączyć bardziej przewidywalne zachowanie aplikacji i kontrolować, czy należy wysyłać dwukierunkowe Resetowanie protokołu TCP (TCP RST) w czasie poza limit czasu bezczynności ruchu wychodzącego. Przejrzyj informacje [o limicie czasu bezczynności na potrzeby resetowania protokołu TCP](https://aka.ms/lbtcpreset) , co obejmuje dostępność regionów.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Zapobieganie łączności wychodzącej

Reguły równoważenia obciążenia zapewniają automatyczne Programowanie dla wychodzącego translatora adresów sieciowych. Jednak niektóre scenariusze mogą korzystać z funkcji automatycznego programowania wychodzącego NAT przez regułę równoważenia obciążenia, co pozwala na kontrolowanie lub Uściślanie zachowania.  
Tego parametru można użyć na dwa sposoby:

1. Opcjonalne pomijanie przy użyciu przychodzącego adresu IP dla wychodzącego elementu Reloader poprzez wyłączenie wychodzącego przystawcy dla reguły równoważenia obciążenia
  
2. Dostosuj parametry wychodzącego ruchu źródłowego dla adresu IP używanego do ruchu przychodzącego i wychodzącego jednocześnie.  Automatyczne wychodzące programowanie NAT musi być wyłączone, aby zezwolić regule wychodzącej na przejęcie kontroli.  Na przykład w celu zmiany alokacji portu podrzędnego adresu reportowego używany również dla ruchu przychodzącego `disableOutboundSnat` parametr musi być ustawiony na wartość true.  Jeśli podjęto próbę użycia reguły wychodzącej w celu ponownego zdefiniowania parametrów adresu IP używanego do obsługi ruchu przychodzącego i nie wydano wychodzącego programowania NAT dla reguły równoważenia obciążenia, operacja konfigurowania reguły wychodzącej zakończy się niepowodzeniem.

>[!IMPORTANT]
> Jeśli ustawisz ten parametr na wartość true, maszyna wirtualna nie będzie mogła mieć łączności wychodzącej, aby zdefiniować łączność wychodzącą.  Niektóre operacje związane z maszyną wirtualną lub aplikacją mogą zależeć od dostępnego połączenia wychodzącego. Zadbaj o to, aby zrozumieć zależności danego scenariusza i wziąć pod uwagę wpływ wprowadzania tej zmiany.

Czasami jest niepożądane, aby maszyna wirtualna mogła utworzyć przepływ wychodzący. Może być również wymagane, aby można było zarządzać miejscami docelowymi z przepływami wychodzącymi lub które mogą zaczynać przepływy przychodzące. W takim przypadku można użyć [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md) do zarządzania miejscami docelowymi, do których maszyna wirtualna może uzyskać dostęp. Można również użyć sieciowych grup zabezpieczeń do zarządzania tym, które publiczne miejsce docelowe może inicjować przepływy przychodzące.

Po zastosowaniu sieciowej grupy zabezpieczeń do maszyny wirtualnej z równoważeniem obciążenia należy zwrócić uwagę na [Tagi usługi](../virtual-network/security-overview.md#service-tags) i [domyślne reguły zabezpieczeń](../virtual-network/security-overview.md#default-security-rules). Musisz się upewnić, że maszyna wirtualna może odbierać żądania sondowania kondycji z Azure Load Balancer.

Jeśli sieciowej grupy zabezpieczeń blokuje żądania sondy kondycji z domyślnego tagu AZURE_LOADBALANCER, sonda kondycji maszyny wirtualnej kończy się niepowodzeniem, a maszyna wirtualna jest oznaczona jako wyłączona. Load Balancer przestaje wysyłać Nowe przepływy do tej maszyny wirtualnej.

## <a name="scenarios-with-outbound-rules"></a>Scenariusze z regułami ruchu wychodzącego

| # | Scenariusz| Szczegóły|
|---|---|---|
| I | Oczyść połączenia wychodzące do określonego zestawu publicznych adresów IP| Możesz użyć reguły ruchu wychodzącego, aby przetworzyć połączenia wychodzące w taki sposób, aby były wyświetlane z określonego zestawu publicznych adresów IP w celu ułatwienia listy dozwolonych scenariuszy.  Ten źródłowy publiczny adres IP może być taki sam, jak używany przez regułę równoważenia obciążenia lub inny zestaw publicznych adresów IP, niż jest używany przez regułę równoważenia obciążenia.  1. Utwórz [publiczny prefiks adresu IP](https://aka.ms/lbpublicipprefix) (lub publiczne adresy IP z prefiksu publicznego adresu IP) 2. Utwórz publiczną usługa Load Balancer w warstwie Standardowa 3. Utwórz frontony odwołujące się do publicznego prefiksu IP (lub publicznych adresów IP), których chcesz użyć 4. Użyj ponownie puli zaplecza lub Utwórz pulę zaplecza i umieść maszyny wirtualne w puli zaplecza Load Balancer publicznej 5. Skonfiguruj regułę ruchu wychodzącego na Load Balancer publicznej, aby zaprogramować wychodzącą translację NAT dla tych maszyn wirtualnych przy użyciu frontonów. Jeśli nie chcesz, aby reguła równoważenia obciążenia była używana dla ruchu wychodzącego, należy wyłączyć wychodzące elementu reźródłowego dla reguły równoważenia obciążenia.
| II | Modyfikuj przydział portu dla współdzielonego| Korzystając z reguł ruchu wychodzącego, można dostroić [automatyczną alokację portu z użyciem puli zaplecza](load-balancer-outbound-connections.md#preallocatedports). Na przykład jeśli masz dwie maszyny wirtualne, które współużytkują jeden publiczny adres IP dla wychodzącego translatora adresów sieciowych, możesz zwiększyć liczbę portów przyznanych z domyślnych portów 1024, jeśli występuje wyczerpanie. Każdy publiczny adres IP może współtworzyć maksymalnie 64 000 portów tymczasowych.  W przypadku skonfigurowania reguły wychodzącej z jednym frontonem publicznego adresu IP można rozpowszechnić łącznie 64 000 portów do maszyn wirtualnych w puli zaplecza.  W przypadku dwóch maszyn wirtualnych maksymalnie 32 000 portów przydziałów adresów sieciowych można przydzielyć za pomocą reguły ruchu wychodzącego (2x 32 000 = 64 000). Za pomocą reguł ruchu wychodzącego można dostroić porty przydzielone domyślnie. Przydzieli się więcej lub mniej niż domyślna alokacja portu przydziałów adresów sieciowych. Każdy publiczny adres IP ze wszystkich frontonów reguły wychodzącej współużytkuje do 64 000 tymczasowych portów do użycia jako porty protokołu reportowego.  Load Balancer przydziela porty przystawek adresów sieciowych w wielokrotnościach 8. Jeśli podano wartość, która nie jest podzielna przez 8, operacja konfiguracji zostanie odrzucona.  Jeśli podjęto próbę przydzielenia większej liczby portów ze standardem adresów IP, które są dostępne w oparciu o liczbę publicznych adresów, operacja konfiguracji zostanie odrzucona.  Na przykład w przypadku przydzielenia 10 000 portów na maszynę wirtualną i 7 maszyn wirtualnych w puli zaplecza współużytkują jeden publiczny 64 000 > 10 000 adres IP  Aby włączyć ten scenariusz, można dodać więcej publicznych adresów IP do frontonu reguły ruchu wychodzącego. Można przywrócić [domyślną alokację portu na podstawie rozmiaru puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) , określając 0 dla liczby portów. W takim przypadku pierwsze wystąpienia maszyn wirtualnych 50 będą mieć 1024 portów, a 51-100 wystąpienia maszyn wirtualnych otrzymają 512 i tak dalej, zgodnie z [tabelą](#snatporttable).|
| IV| Włącz tylko wychodzące | Aby zapewnić wychodzące NAT dla grupy maszyn wirtualnych, można użyć usługa Load Balancer w warstwie Standardowa publicznej. W tym scenariuszu można użyć reguły wychodzącej sama przez siebie bez potrzeby stosowania dodatkowych reguł.|
| V | Wychodzące NAT tylko dla maszyn wirtualnych (bez ruchu przychodzącego) | Zdefiniuj usługa Load Balancer w warstwie Standardowa publiczny, umieść maszyny wirtualne w puli zaplecza i skonfiguruj regułę ruchu wychodzącego w celu zaprogramowania wychodzących NAT i oczyść połączenia wychodzące z określonego publicznego adresu IP. Możesz również użyć publicznego prefiksu IP Uprość białe, aby wyświetlić listę źródeł połączeń wychodzących. 1. Utwórz publiczną usługa Load Balancer w warstwie Standardowa. 2. Utwórz pulę zaplecza i umieść maszyny wirtualne w puli zaplecza Load Balancer publicznej. 3. Skonfiguruj regułę ruchu wychodzącego na Load Balancer publicznej w celu zaprogramowania wychodzącego translatora adresów sieciowych dla tych maszyn wirtualnych.
| V| Wychodzące translatory adresów sieciowych dla wewnętrznych scenariuszy usługa Load Balancer w warstwie Standardowa| W przypadku korzystania z usługa Load Balancer w warstwie Standardowa wewnętrznego, wychodzący NAT nie jest dostępny do momentu jawnego zadeklarowania łączności wychodzącej. Możesz zdefiniować łączność wychodzącą przy użyciu reguły wychodzącej, aby utworzyć łączność wychodzącą dla maszyn wirtualnych za wewnętrzną usługa Load Balancer w warstwie Standardowa, wykonując następujące kroki: 1. Utwórz publiczną usługa Load Balancer w warstwie Standardowa. 2. Utwórz pulę zaplecza i umieść maszyny wirtualne w puli zaplecza Load Balancer publicznej oprócz wewnętrznego Load Balancer. 3. Skonfiguruj regułę ruchu wychodzącego na Load Balancer publicznej w celu zaprogramowania wychodzącego translatora adresów sieciowych dla tych maszyn wirtualnych. Więcej informacji o tym scenariuszu można znaleźć w [tym przykładzie](https://docs.microsoft.com/azure/load-balancer/egress-only). |
| VI | Włącz protokoły UDP & protokołu TCP dla ruchu wychodzącego NAT z usługa Load Balancer w warstwie Standardowa publiczny | W przypadku korzystania z usługa Load Balancer w warstwie Standardowa publicznego automatyczne wychodzące programowanie NAT jest zgodne z protokołem transportu reguły równoważenia obciążenia. 1. Wyłącz przychodzący magazyn danych w regule równoważenia obciążenia. 2. Skonfiguruj regułę wychodzącą na tym samym Load Balancer. 3. Ponownie Użyj puli zaplecza już używanej przez maszyny wirtualne. 4. Określ "Protokół": "All" jako część reguły ruchu wychodzącego. Gdy są używane tylko reguły NAT dla ruchu przychodzącego, nie jest dostarczany wychodzący translator adresów sieciowych. 1. Umieść maszyny wirtualne w puli zaplecza. 2. Zdefiniuj co najmniej jedną konfigurację adresu IP frontonu z publicznymi adresami IP lub publicznym prefiksem adresu IP 3. Skonfiguruj regułę wychodzącą na tym samym Load Balancer. 4. Określ "Protocol": "All" jako część reguły ruchu wychodzącego |


## <a name="limitations"></a>Ograniczenia

- Maksymalna liczba użytecznych portów tymczasowych na adres IP frontonu to 64 000.
- Zakres konfigurowalnego limitu czasu bezczynności wynosi od 4 do 120 minut (240 do 7200 sekund).
- Load Balancer nie obsługuje protokołu ICMP dla wychodzącego translatora adresów sieciowych.
- Reguły ruchu wychodzącego mogą być stosowane tylko do podstawowej konfiguracji adresu IP karty sieciowej.  Nie można utworzyć reguły ruchu wychodzącego dla pomocniczego adresu IP maszyny wirtualnej lub urządzenie WUS. Obsługiwane są wiele kart sieciowych.
- Role procesów roboczych sieci Web bez sieci wirtualnej i innych usług platformy firmy Microsoft mogą być dostępne, gdy jest używany tylko wewnętrzny usługa Load Balancer w warstwie Standardowa ze względu na efekt uboczny od sposobu, w jaki działają usługi frontonu i inne usługi platformy. Nie należy polegać na tym efekcie ubocznym, ponieważ sama sama usługa lub bazowa platforma mogą ulec zmianie bez powiadomienia. Zawsze należy założyć, że konieczne jest utworzenie połączenia wychodzącego jawnie, jeśli jest to potrzebne, przy użyciu tylko wewnętrznego usługa Load Balancer w warstwie Standardowa. Scenariusz 3 opisany w tym artykule nie jest dostępny.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Zapoznaj się z [często zadawanymi pytaniami dotyczącymi Azure Load Balancer](load-balancer-faqs.md).
- Dowiedz się więcej o [regułach ruchu wychodzącego](load-balancer-outbound-rules-overview.md) dla standardowej Load Balancer publicznej.
- Dowiedz się więcej o [Load Balancer](load-balancer-overview.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
- Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) na platformie Azure.
